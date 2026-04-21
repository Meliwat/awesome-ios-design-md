# Design System Inspiration of Netflix (iOS)

## 1. Visual Theme & Atmosphere

Netflix's iOS app is a cinematic dark canvas where poster art does all the heavy lifting. From the moment you pick a profile, you're dropped into a screen that feels less like a traditional app and more like a black-box theater: the canvas is `#141414` (a warm true-dark charcoal used across every Netflix surface since the brand's modern refresh), and every row of content is a horizontal strip of 2:3 vertical poster tiles separated only by titles and the faintest of whitespace. The top of every Home screen is a full-bleed hero — no app-logo header, no search bar at the top — where a trailer auto-plays muted over the selected title's key art, fading from the full landscape bleed at the top into the dark canvas via a vertical gradient that eats away at the bottom third of the image. You scroll, and rows of posters flow under your thumb like film strips.

The accent system is singular and sacred: Netflix Red (`#E50914`). It appears in exactly three places — the word "NETFLIX" at the top of the feed (the only header element, a wordmark), the large red Play button on the hero and every detail page, and the notification badges. Nothing else in the UI is red. Everything else — text, surface elevation, buttons that aren't Play — lives in a narrow monochrome palette: white (`#FFFFFF`), muted white (`#AAAAAA`), a near-black deep gray (`#1F1F1F`) for raised surfaces, and the canvas (`#141414`) itself. When Netflix wants to signal a different profile, it rotates a small palette of saturated accent colors (red, blue, yellow, green) on the circular avatars of the profile picker — but those never leak into the main app chrome.

Typography is Netflix Sans, a proprietary sans-serif designed in 2018 by Dalton Maag to replace Gotham across the product. It's a humanist geometric with tight tracking, designed to read as brand-owned and cinematic. The hierarchy is display-heavy because Netflix has a habit of putting 24–48pt titles on top of poster art, and compact at the UI level because content takes the focus. The most expressive moment is on the title detail page, where the show's logo artwork — rendered as an image, not type — sits at the top at 60%+ width, tightly followed by descriptive metadata in 14–15pt regular. Netflix Sans plays a supporting role; the art is the headline.

**Key Characteristics:**
- True-dark canvas (`#141414`) — a warm charcoal, not pure black; behind hero art it goes `#000000`
- **Dark mode only** — no light mode; Netflix has never shipped a light-mode iOS app
- Netflix Red (`#E50914`) reserved for the logo wordmark, Play button, and notification badges — nothing else
- Full-bleed hero at the top of Home with muted trailer auto-play and a gradient fade into canvas
- Horizontal rows of 2:3 portrait poster tiles — 60+ rows on a typical Home
- **"Top 10" numbered row** — giant side-profile numerals (1, 2, 3, …) rendered as a background layer with the poster sitting in front
- "Continue Watching" row shows a red progress bar at the bottom of each poster
- Netflix Sans (proprietary) paired with Graphik fallback, weights 400 / 500 / 700
- Profile selector on launch — circular avatars, colored rotation, "Who's watching?" prompt
- Mobile tab bar: Home, New & Hot, My Netflix, Downloads (if offline content exists)
- Scrubber with full-size preview thumbnails on video playback
- "Are you still watching?" inactivity modal during extended playback

## 2. Color Palette & Roles

### Primary
- **Netflix Red** (`#E50914`): The iconic saturated red — logo wordmark at top of Home, the main Play button, notification badges, "LIVE" event indicators, Continue Watching progress bar fill. Not used for secondary actions, borders, or generic accents.
- **Netflix Red Pressed** (`#B7070F`): Pressed / active-state variant for red CTAs.
- **Netflix Red Dimmed** (`#831010`): Very occasionally used for inactive or loading-state placeholders on red elements.

### Canvas & Surfaces
- **Canvas** (`#141414`): Primary dark canvas — Netflix's signature "warm dark," used across Home, browse, profile, settings, everywhere.
- **Deep Black** (`#000000`): Full-bleed background behind title art, player background, loading-state fills.
- **Surface 1** (`#1F1F1F`): Card backgrounds when a card appears in front of the canvas, genre-picker grid tiles.
- **Surface 2** (`#2A2A2A`): Bottom sheets, modal backgrounds, pressed states on rows.
- **Surface 3** (`#3A3A3A`): Elevated sheets, context menus, sign-in fields.
- **Divider** (`#2B2B2B`): 1pt hairlines between rows inside settings / profile screens.
- **Input Field** (`#333333`): Sign-in / search input backgrounds.

### Text
- **Text Primary** (`#FFFFFF`): Titles, headlines, primary UI text.
- **Text Secondary** (`#AAAAAA`): Metadata — rating, year, runtime, genres, "Season 1 · 10 episodes".
- **Text Tertiary** (`#777777`): Disabled text, lowest-emphasis labels.
- **Text on Red Button** (`#FFFFFF`): White text on the red Play button.
- **Text on Gray Button** (`#000000`): On the white/gray "My List" secondary button, text goes black (intentional contrast).

### Profile Accent Rotation
Profiles cycle through a small set of saturated hues on their circular avatar backdrops:
- **Profile Red** (`#E50914`) — primary
- **Profile Blue** (`#3E3E91`) — warm navy
- **Profile Yellow** (`#F5D85C`) — mustard yellow
- **Profile Green** (`#4B8A3E`) — forest green
- **Kids Profile Orange** (`#F8981D`) — kids profile signature

These colors appear only on the profile-picker screen; they never bleed into the main app chrome.

### Semantic
- **Info / Link** (`#54B9C5`): Rarely used; Netflix prefers underlined text for links.
- **Error** (`#E50914`): Same token as brand red for playback errors.
- **Success / Downloaded** (`#AAAAAA`): Downloaded indicator on My Netflix uses gray, not green — Netflix avoids green semantics entirely.

### Dark-Only
Netflix iOS has no light mode. The app has been dark-only since its modern launch; even system light mode preference is ignored and the app remains `#141414`.

## 3. Typography Rules

### Font Family
- **Primary**: `Netflix Sans` (proprietary, designed in 2018 by Dalton Maag — replaced Gotham across Netflix's product, marketing, and UI)
- **Fallback Web / Secondary**: `Graphik` (Netflix's prior display workhorse, still referenced in some older marketing assets)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Graphik', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: System — PingFang SC, Hiragino Sans, Apple SD Gothic Neo, Noto Sans Arabic / Hebrew / Devanagari / Thai
- Netflix Sans ships with Regular, Medium, Bold, and Black weights; the app uses Regular, Medium, and Bold

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Title Detail Hero | Netflix Sans | 32pt | 700 | 1.1 | -0.4pt | Show / movie title below the hero art (when art logo not available) |
| Section Row Header | Netflix Sans | 17pt | 700 | 1.2 | -0.1pt | "Continue Watching", "Trending Now" row headers |
| Screen Title | Netflix Sans | 20pt | 700 | 1.2 | -0.2pt | "My Netflix", "Search", "Downloads" |
| Episode Title | Netflix Sans | 17pt | 500 | 1.2 | 0pt | "Episode 1: Chapter 1" on episode list |
| Body | Netflix Sans | 14pt | 400 | 1.45 | 0pt | Synopsis, description, about sections |
| Metadata | Netflix Sans | 13pt | 400 | 1.3 | 0pt | "2024 · 2h 15m · TV-MA" on title detail |
| Metadata Secondary | Netflix Sans | 12pt | 400 | 1.3 | 0pt | Genre tags, "Watched 5 days ago" |
| Button (Primary Play) | Netflix Sans | 17pt | 700 | 1.0 | 0pt | Red Play button label |
| Button (Secondary) | Netflix Sans | 15pt | 500 | 1.0 | 0pt | My List, Info, Download |
| Badge | Netflix Sans | 11pt | 700 | 1.0 | 0.4pt | "NEW SEASON", "TOP 10" chips, UPPERCASE |
| Tab Label | Netflix Sans | 10pt | 500 | 1.0 | 0.2pt | Bottom tab labels |
| Rating / Cert | Netflix Sans | 12pt | 700 | 1.0 | 0pt | "TV-MA", "HD", "Dolby Atmos" badges |
| Progress Time | Netflix Sans | 12pt | 400 | 1.0 | 0pt | Time remaining below Continue Watching |

### Principles
- **Weights at 400 / 500 / 700**: Regular for body, Medium for episode titles and secondary buttons, Bold for headlines and Play button
- **Display moments use show artwork, not type**: Title detail hero prefers the show's branded logo-image; Netflix Sans type kicks in only when the image is unavailable
- **Negative tracking on titles**: Title detail hero is -0.4pt, other titles -0.1 through -0.2pt
- **Dynamic Type respected on reading surfaces**: Synopsis, settings, about, profile info; fixed on row headers, buttons, and badges
- **Badges and certificates stay all-caps**: "NEW", "TOP 10", "HD", "DOLBY ATMOS" — uppercase 11–12pt weight 700 with +0.4pt tracking
- **No italic, no underlines**: Netflix Sans does not have italic in the app's usage; links use red underline only on web, not iOS

## 4. Component Stylings

### Buttons

**Primary Play Button (The Iconic Red Pill)**
- Shape: Full-width pill, 48pt tall, corner radius 4pt (NOT full pill — Netflix uses soft 4pt corners, a signature)
- Background: `#E50914`
- Icon: SF Symbol `play.fill` in `#FFFFFF`, 20pt weight 700, leading the text
- Text: "Play" or "Resume" in Netflix Sans 17pt weight 700, `#FFFFFF`
- Padding: 14pt vertical, 24pt horizontal
- Pressed: background `#B7070F`, scale 0.98, medium haptic
- Width: typically full-width on title detail below the hero, or 160pt on an inline CTA
- This button is the app's visual anchor — it's almost always the single most saturated element on any screen

**Secondary Button ("My List", "Info", "Trailer")**
- Background: `rgba(255,255,255,0.15)` (semi-transparent white for glass-like effect on dark canvas) OR solid `#2A2A2A` depending on context
- Text: `#FFFFFF` Netflix Sans 15pt weight 500
- Leading SF Symbol glyph 16pt (`plus` for My List, `info.circle` for Info, `play.rectangle` for Trailer)
- Corner radius: 4pt (same as Play button)
- Padding: 12pt vertical, 20pt horizontal
- Pressed: `rgba(255,255,255,0.25)` / `#3A3A3A`

**Download Button**
- Shape: icon-only button (no background)
- Default: SF Symbol `arrow.down.to.line` 24pt `#FFFFFF`
- Downloading: animated progress arc around the icon, cyan `#54B9C5` accent
- Downloaded: SF Symbol `checkmark.circle.fill` 24pt `#AAAAAA` (intentionally gray — not green — Netflix's color choice)
- Tap while downloaded: opens actions menu (Delete, Watch)

**My List Plus Button (Add/Remove)**
- Default (not added): SF Symbol `plus` 24pt `#FFFFFF`
- Added: SF Symbol `checkmark` 24pt `#FFFFFF` (NOT green — plain white)
- Animation: plus rotates 90° + cross-fades to checkmark over 200ms
- Haptic: success on add

**Icon Only (Share / Rate / Rate Down / More)**
- Glyph: 24pt white SF Symbol
- Hit area: 44pt × 44pt
- Default: `#FFFFFF`
- Pressed: `#AAAAAA`
- Labels below icons: Netflix Sans 12pt weight 400 `#AAAAAA`

### Cards & Containers

**Poster Tile (THE atomic unit)**
- Aspect: **2:3 portrait** (key art — the vertical poster format)
- Standard tile width: 130pt on iPhone, 120pt on SE, 160pt on Pro Max, 200pt on iPad
- Corner radius: **4pt** (Netflix's signature soft-but-not-rounded corner)
- Background: `#1F1F1F` (shows while art loads)
- Image: full-bleed within the tile, no border, no shadow
- Gap between tiles in a row: 8pt
- Tap: fade-to-detail transition with shared-element of the poster
- Preload: fetch a trailer thumbnail along with the poster art for instant-play on hover / detail open

**Hero Card (Top of Home)**
- Full-width, positioned directly below the "NETFLIX" wordmark at the top
- Height: ~70% of screen height on Home (varies by device)
- Image: full-bleed landscape key art OR auto-playing muted trailer video
- Overlay at bottom: a vertical gradient from `rgba(0,0,0,0)` (top) to `#141414` (bottom) across the lower 40% — makes the hero fade into the canvas
- Below-gradient content (inside the hero frame):
  - Title logo image (if available) at 60% width, centered or left-aligned
  - Genre chips row: inline pills "Gripping · Dramas · Thrillers" in 13pt weight 500 `#FFFFFF` separated by `•`
  - Button row: [My List (secondary)] [▶ Play (red primary)] [Info (secondary)]
- Trailer auto-play: starts muted after 2s delay on scroll-stop, pauses when off-screen
- Audio toggle (small speaker icon 20pt) floats top-right of hero area

**Row (Horizontal Scroll of Posters)**
- Section header: Netflix Sans 17pt weight 700 `#FFFFFF`, 16pt leading, 8pt top / 12pt bottom margin
- Row content: horizontal scroll of 2:3 poster tiles, 8pt gap
- End of row peek: last tile is partially visible off-screen to signal scroll
- Row height: driven by tile size (e.g. 130pt × 1.5 = 195pt for tiles)
- Most rows show 20–40 titles; the row does not typically loop

**"Top 10" Row (Signature Component)**
- Structurally like any other row but with a **giant numeral** rendered to the left of each tile
- Numerals are displayed in a heavy outlined style (2pt stroke) at ~160pt tall, colored `#141414` with a slight opacity gradient, positioned behind the poster so the poster "covers" the inner edge of the numeral
- Numerals: 1, 2, 3, …, 10 — always the Top 10 in the user's country
- Typography: special condensed display face (sometimes referred to as "Top 10 Bebas" in Netflix's internal materials) — a compressed heavy sans with exaggerated stroke contrast
- This is visually the most distinctive row in the app

**Continue Watching Tile**
- Same 2:3 poster format but with:
  - Red progress bar at the **bottom** of the tile: 2pt tall, track `rgba(255,255,255,0.3)`, fill `#E50914`, width = % watched
  - Title text below the poster (14pt weight 500 white) — one of the few rows with text labels under tiles
  - Optional "Episode X of Y" metadata in 12pt weight 400 secondary

**Title Detail Screen**
- Top: a 16:9 hero image of the show/movie (full-width, no corner radius)
- Below the hero: a semi-transparent `rgba(0,0,0,0.5)` vertical gradient that fades into the canvas
- Title logo (show branding, usually an image) at 65% width centered
- Metadata row: Year · Runtime · Rating certificate (e.g. "2024 · 1h 48m · TV-MA")
- Genre row: inline chips separated by `•`
- Play button (red, full width)
- Secondary buttons row: My List / Info / Share icons with labels
- Synopsis: 14pt weight 400 body, 3-line clamp with "more" trailing
- Cast row: horizontal strip of circular actor avatars with names below
- Episode list (for series): list of episode rows with episode image thumbnail + title + description
- "More Like This" row: 3×3 grid of 2:3 posters below episodes
- Details block: director, cast, year, genre, rating — collapsible

**Episode Row (On Series Detail)**
- Height: ~100pt
- Layout: 16:9 episode thumbnail (160pt wide) on left + stacked title/description on right
- Thumbnail has a small centered play triangle overlay on hover-equivalent state
- Download icon trailing right (24pt, state-aware)
- Progress bar: if partially watched, a red progress bar at the bottom edge of the thumbnail

**Profile Picker Card (Launch Screen)**
- Appears on app launch — full-screen "Who's watching?" title
- 2-column grid of profile tiles (some layouts use 1 row of 4)
- Each profile:
  - Circular avatar 80pt diameter, corner radius 50%
  - Colored background (red, blue, yellow, green, kids-orange rotation)
  - 2pt white border on hover-equivalent state
  - Name below: Netflix Sans 14pt weight 500 white
- "Add Profile" option: outline-dashed circle with `+` icon
- "Manage Profiles" link at bottom

### Navigation

**Top App Bar (Home only — the Netflix wordmark)**
- Height: 44pt + safe area
- Background: transparent at top of feed, fades to `rgba(20,20,20,0.92)` with `.regularMaterial` blur on scroll
- Contents (left-aligned): the red "NETFLIX" wordmark (a PNG / vector image, NOT type) 22pt tall
- Trailing: category chip row ("TV Shows", "Movies", "My List") 32pt pills — see below
- On scroll-down: bar stays, chips hide
- The NETFLIX wordmark is the ONLY header on Home — no search bar, no profile icon here (search moves to the tab bar as "Search")

**Top Category Chip Row (below wordmark)**
- Pills for "TV Shows", "Movies", "My List"
- Pill: height 32pt, corner radius 500pt, border `rgba(255,255,255,0.4)` 1pt, text `#FFFFFF` 14pt weight 500
- Tapping a pill filters the entire feed to that category (swapping the visible rows and hero)
- No fill on selection — just a pulse animation

**Bottom Tab Bar**
- Height: 48pt + safe area
- Background: `rgba(20,20,20,0.92)` with `.regularMaterial` blur, 1pt top hairline `#2B2B2B`
- 4 tabs: **Home** (house icon), **New & Hot** (play-circle icon), **My Netflix** (person-circle icon — replaced "Downloads" in 2024+ for most accounts), **Downloads** (downloaded-arrow icon — only shown if downloaded content exists)
- Icon size: 24pt, 44pt hit area
- Active: `#FFFFFF` with filled SF Symbol variant
- Inactive: `#777777` with outlined variant
- Labels: Netflix Sans 10pt weight 500, shown always
- No center Create button — Netflix is view-only

### Input Fields

**Search Input (Search Tab)**
- Background: `#2A2A2A`
- Height: 40pt
- Corner radius: 4pt
- Leading 18pt `magnifyingglass` SF Symbol `#AAAAAA`
- Placeholder: "Search for shows, movies, genres" Netflix Sans 16pt weight 400 `#AAAAAA`
- Focus: background `#333333`, cursor white, placeholder fades
- Trailing: `xmark.circle.fill` 18pt `#AAAAAA` when text is present

**Sign-in Field (Login)**
- Background: `#333333`
- Height: 56pt
- Corner radius: 4pt
- Floating label pattern — label starts as placeholder, animates to top-small on focus
- Text: `#FFFFFF` 16pt weight 400
- Validation: error state border `#E50914` with error message below

### Distinctive Components

**The NETFLIX Wordmark Header**
- The ONLY element at the top of the Home screen when first opened
- Red (`#E50914`) vector wordmark, uppercase compressed sans, 22pt tall
- No accompanying text, no icons — Netflix's confidence play: "you know what this app is"

**Hero Trailer Auto-Play**
- Trailer video plays muted behind the hero after 2–3s delay
- Audio-toggle speaker icon in upper-right (tap to unmute)
- Smoothly fades between trailer and poster key art
- Pauses when app backgrounds or when user scrolls past
- Extremely distinctive Netflix UX moment

**"Top 10" Numerals**
- Giant outlined numerals (1, 2, 3, …) behind each poster in the Top 10 row
- Numeral is ~160pt tall, thin 2pt stroke, color `#141414` (canvas color) with subtle internal lightening
- Poster sits in front of the numeral with the numeral partially obscured by it
- Unique to Netflix — this row composition is instantly recognizable

**Continue Watching Progress Bar**
- Red horizontal bar at the bottom of a poster, 2pt tall
- Fills based on watch progress (0–100%)
- Only appears on Continue Watching tiles

**"Are you still watching?" Modal**
- Full-screen modal that appears after ~3 episodes of autoplay without user interaction
- Shows the most recent episode's thumbnail
- Text: "Are you still watching {show name}?"
- Two buttons: "Continue Watching" (red primary) / "Exit"
- Dimmer: `rgba(0,0,0,0.8)`

**Fast-Forward / Scrub with Preview Thumbnails**
- On the player, dragging the scrubber reveals a strip of preview thumbnails above the handle
- Thumbnails: 140pt × 80pt (16:9) snapshots every 10 seconds of runtime
- Shown in a horizontal strip above the scrubber, center thumbnail represents current scrub target
- Current timestamp shown as a "XX:XX / XX:XX" label in 12pt weight 400

**Rating Up / Rating Down (Thumbs)**
- Replaced star ratings in 2017
- Thumbs up / double-thumbs-up ("Love this!") / thumbs down
- Double-tap on thumbs-up triggers the "Love this!" overlay — a red heart-thumb combo animation
- Haptic: medium on thumb-up/down, heavy success on double-thumbs

**Mobile Game Shelf (New & Hot / My Netflix)**
- Netflix Games get their own horizontal row
- 1:1 square tiles with larger corner radius (12pt)
- "GAME" badge in the corner (uppercase 11pt weight 700 white on red chip)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 48, 56, 64
- Content margin: 16pt horizontal on text content, 0pt on row scrolls (tiles extend to edges)
- Between rows: 12pt top / 8pt bottom on section headers
- Between poster tiles in a row: 8pt

### Grid & Container
- Feed: single-column vertical stack of horizontal rows; tiles flow horizontally
- Title detail: single-column 16pt horizontal margin on text, full-width on hero image
- Episode list: single-column rows
- Profile picker: 2×2 or 1×4 grid depending on number of profiles
- iPad landscape: larger tiles, same row structure

### Whitespace Philosophy
- **Tight vertical density in feed**: Row headers are only 8pt below posters above; rows pack close so more content is visible
- **Generous on detail**: Title detail uses 16pt horizontal and 20pt between sections — reading surface
- **Zero margin on hero**: Hero image is full-bleed, gradient fade does the visual separation work

### Border Radius Scale
- Square (0pt): Hero images, episode thumbnails
- Signature (4pt): Poster tiles, Play button, secondary buttons, inputs — THE Netflix soft corner
- Standard (8pt): Download progress arc on icon
- Comfortable (12pt): Game tiles (1:1), bottom sheets
- Full Pill (500pt): Category chips in top bar
- Circle (50%): Profile avatars, download arc, actor headshots

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed, rows, posters, most UI |
| Hero Gradient | Vertical gradient `rgba(0,0,0,0)` → `#141414` over 40% | Fades hero trailer into canvas |
| Card Lift (Level 1) | `rgba(0,0,0,0.5) 0 4px 16px` | Profile avatars when hovered / selected |
| Sheet (Level 2) | `rgba(0,0,0,0.6) 0 -12px 40px` | Bottom sheets (share, downloads, overflow) |
| Player Chrome | `rgba(0,0,0,0.6)` gradient overlay over video | Darkens edges of player for control visibility |
| Modal Dim (Level 3) | `rgba(0,0,0,0.8)` + optional backdrop-blur | "Are you still watching?" modal, sign-out dialogs |
| Tab Bar Material | `.regularMaterial` over `rgba(20,20,20,0.92)` | Bottom tab bar blur |

**Shadow Philosophy**: Netflix is almost entirely flat because the dark canvas does the job of separating elements through contrast alone. The signature "lift" moment is the hero gradient, which isn't a shadow but a color-to-canvas fade, giving the hero its cinematic quality without needing elevation. Actual shadows only appear in two places: behind raised elements like player chrome gradients and sheet surfaces, and (very subtly) on selected profile avatars. Posters and tiles never carry shadow — they sit flat on the canvas.

### Motion
- **Play button tap**: Scale 0.98 with medium haptic; if playback starts, the player slides up from bottom in 0.3s spring
- **Poster tap → Detail**: Shared-element transition on the poster image from its row position to the detail screen hero, 0.35s spring damping 0.85
- **Hero trailer transition**: Fade from poster image to trailer video over 600ms after 2s idle
- **My List add/remove**: Plus-to-checkmark cross-fade + rotate 90° over 200ms, success haptic
- **Row horizontal scroll**: 60fps swipe with paging, momentum decay
- **Episode autoplay → Next**: 10s countdown ring appears in bottom-right after an episode ends; tap cancels, otherwise auto-transitions with fade
- **Profile select**: Selected avatar scales 1.0 → 1.1 → 1.0 with a spring, then full-screen fades to Home
- **Rating thumb tap**: Heavy haptic, 1.0 → 1.2 → 1.0 scale bounce, fill swap
- **Scrubber drag preview**: Thumbnails slide horizontally matching scrub position, 60fps

## 7. Do's and Don'ts

### Do
- Use `#141414` as the canvas — warm dark charcoal, not pure black (except behind hero art)
- Reserve Netflix Red (`#E50914`) for the NETFLIX wordmark, Play button, and Continue Watching progress bar — nothing else
- Use the red wordmark as the ONLY header on Home — no text title, no search bar in the top app bar
- Render posters at a 2:3 aspect ratio with a 4pt corner radius — Netflix's signature tile
- Use the vertical gradient from canvas to transparent to fade hero images into the background
- Use `#AAAAAA` for all metadata / secondary text — never gray-blue or warm gray
- Use Netflix Sans for all type, weights 400 / 500 / 700 only
- Auto-play muted hero trailers after a 2s idle delay
- Show the big outlined numerals behind Top 10 posters
- Show the red progress bar on Continue Watching tiles at the bottom edge
- Use the rounded 4pt corner on the Play button (soft corner, not pill)
- Use a 1:4 profile picker grid with colored circular avatars on app launch
- Use "Are you still watching?" modal after 3+ hours of continuous autoplay

### Don't
- Don't use pure black `#000000` as the canvas — `#141414` is warmer and the Netflix standard
- Don't ship a light mode — Netflix has always been dark-only
- Don't use green for success (downloaded, checkmark states) — Netflix uses gray
- Don't use a full-pill corner on the Play button — 4pt is signature
- Don't use red anywhere other than the defined brand moments — no red borders, no red text, no red accent on metadata
- Don't put a title text header on the Home screen — the NETFLIX wordmark image replaces it
- Don't use shadows on poster tiles — they sit flat on the canvas
- Don't auto-play hero trailers with sound — they must start muted with user-controlled unmute
- Don't forget the outlined numerals on the Top 10 row — that visual is the row's defining feature
- Don't animate aggressively — Netflix motion is subtle, 250–400ms springs
- Don't show episode numbers before title — format is "Episode 1: Chapter 1" (episode then title)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Poster tiles 120pt wide; hero image scales to 260pt tall |
| iPhone 13/14/15 | 390pt | Standard 130pt posters; 400pt hero height |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated at top of wordmark |
| iPhone 15/16 Pro Max | 430pt | 160pt posters; 460pt hero; Top 10 numerals at 200pt tall |
| iPad portrait | 768pt | 3-tile visible rows; wordmark left, chips right |
| iPad landscape | 1024pt | 4–5 tile rows visible; hero at 720pt tall |

### Dynamic Type
- Synopsis, settings, About: full scale
- Title detail hero title: scales to 40pt max
- Row headers, Play button label: fixed size
- Poster titles (Continue Watching only): fixed size (tile-sensitive)
- Tab labels: fixed
- Metadata: scales up to 20% max

### Orientation
- Home / Browse / Profile / Search / My Netflix: **portrait-locked**
- Video player: **rotates to landscape for fullscreen**
- Title detail: portrait-locked
- Trailer preview: portrait on title detail (embedded), landscape in full player

### Touch Targets
- Play button: 48pt tall — exceeds 44pt minimum
- Poster tile: full tile tappable (~130pt × 195pt)
- Overflow / more icons: 44pt hit
- Tab bar icons: 24pt glyph with 44pt hit
- Scrubber thumb: 24pt draggable

### Safe Area Handling
- Top: Wordmark respects safe area / Dynamic Island — positioned 12pt below Island on Pro devices
- Bottom: Tab bar respects home indicator
- Player (landscape fullscreen): ignores safe area for video, respects for controls
- Hero image: extends under Dynamic Island (content behind glass); NETFLIX wordmark sits above

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#141414`
- Deep black (hero bg): `#000000`
- Surface 1 (cards): `#1F1F1F`
- Surface 2 (sheets): `#2A2A2A`
- Surface 3 (elevated): `#3A3A3A`
- Divider: `#2B2B2B`
- Input field: `#333333`
- Text primary: `#FFFFFF`
- Text secondary: `#AAAAAA`
- Text tertiary: `#777777`
- Netflix Red: `#E50914`
- Red pressed: `#B7070F`
- Profile rotation: red `#E50914`, blue `#3E3E91`, yellow `#F5D85C`, green `#4B8A3E`, kids-orange `#F8981D`

### Example Component Prompts
- "Create a SwiftUI Netflix Play button: full-width pill, 48pt tall, 4pt corner radius, background `#E50914`. Leading SF Symbol `play.fill` 20pt white, followed by 8pt gap, text 'Play' in Netflix Sans 17pt weight 700 `#FFFFFF`. 14pt vertical / 24pt horizontal padding. Pressed: background `#B7070F`, scale 0.98 with medium haptic."
- "Build a Netflix poster tile: 2:3 portrait aspect, width 130pt height 195pt. Corner radius 4pt. Image fills the tile, background `#1F1F1F` while loading. No border, no shadow. On tap: shared-element transition to title detail hero, 0.35s spring damping 0.85."
- "Design the Netflix hero card: full-width, 400pt tall at top of Home. 16:9 landscape key art image bleeds to screen edges. Vertical gradient overlay in the lower 40% from `rgba(0,0,0,0)` to `#141414` — fades hero into canvas. At the bottom of the gradient: centered title logo image (65% width), then genre pills row '• Gripping • Thriller • Drama' in 13pt weight 500 white, then button row [My List secondary] [▶ Play red primary] [Info secondary]. Trailer auto-play starts after 2s idle, muted. Audio toggle speaker icon top-right."
- "Create the Netflix 'Top 10' row: horizontal scroll of 2:3 posters identical to regular rows, but with a giant outlined numeral behind each poster. Numeral: 1 through 10, ~160pt tall, compressed heavy sans (Bebas-like), 2pt stroke, color `#141414` (canvas color) with slight internal lightening. Poster sits in front of numeral, partially covering its inner edge. 8pt gap between poster+numeral groups."
- "Build a Continue Watching tile: 2:3 poster at 130×195pt, 4pt corner radius. At the bottom of the poster, overlay a horizontal progress bar 2pt tall, track `rgba(255,255,255,0.3)`, fill `#E50914`, width = watch progress %. Below poster: title in Netflix Sans 14pt weight 500 white (1-line clamp), metadata 'Episode 3 of 10' in 12pt weight 400 `#AAAAAA`."
- "Design the Netflix profile picker screen: full-screen `#141414` canvas. Title 'Who's watching?' in Netflix Sans 32pt weight 700 white, centered. 2×2 grid of profile tiles (or 1×4 on fewer profiles). Each profile: 80pt circular avatar with colored background (rotate through red, blue, yellow, green, kids-orange), profile image or SF Symbol center. 16pt gap below: profile name Netflix Sans 14pt weight 500 white. 'Add Profile' tile: dashed outline circle + plus icon. Selected: avatar scales 1.0 → 1.1 → 1.0 spring, then full-screen fades to Home."
- "Create the Netflix bottom tab bar: 48pt + safe area, 4 tabs (Home, New & Hot, My Netflix, Downloads if offline content exists). Background `rgba(20,20,20,0.92)` with `.regularMaterial` blur, 1pt top hairline `#2B2B2B`. Icons 24pt SF Symbols, active `#FFFFFF` filled variant, inactive `#777777` outlined. Labels Netflix Sans 10pt weight 500 shown always. Red notification dot `#E50914` 8pt on icon top-right when applicable."

### Iteration Guide
1. Canvas is `#141414` — a warm dark charcoal, never `#000000` except behind hero art
2. Netflix Red `#E50914` is for the logo wordmark, the Play button, and the Continue Watching progress bar — nothing else
3. Posters are 2:3 portrait with 4pt corner radius — not rounded, not square
4. Hero image fades into canvas with a bottom-up vertical gradient — the hallmark cinematic moment
5. No light mode — ever. Netflix is dark-only.
6. Typography: Netflix Sans only, weights 400 / 500 / 700
7. No app title on Home — the red NETFLIX wordmark image IS the header
8. Top 10 row uses giant outlined numerals behind each poster — that's the row's visual signature
9. Continue Watching tiles show a red progress bar at the bottom — that's the row's visual signature
10. Secondary buttons use `rgba(255,255,255,0.15)` semi-transparent white or `#2A2A2A` solid
11. Tab bar has 4 tabs (not 5) — no Create / upload concept
12. Trailer auto-play starts muted after 2s idle — unmute only on user tap
13. Gray is used for "downloaded" / "success" checkmarks — never green
14. Haptics: medium on Play, success on My List add, heavy on double-thumbs-up Love, selection on chip tap
