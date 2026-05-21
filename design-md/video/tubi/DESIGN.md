# Design System Inspiration of Tubi (iOS)

## 1. Visual Theme & Atmosphere

Tubi's iOS app is built around a single, unmistakable idea: **free movies and TV, paid for by ads, presented like a premium streamer**. The canvas is a deep indigo-black (`#0A0A2A`) — not the neutral near-black of Netflix or the warm charcoal of Apple TV, but a cool, slightly violet darkness that exists specifically so Tubi's signature **purple→magenta gradient (`#7408FF` → `#FF00FF`)** can glow against it. That gradient is the entire brand. It runs through the lowercase `tubi` wordmark, the focus and selection states, the active bottom-tab icon, continue-watching progress bars, and the secondary CTAs. Where most streaming apps pick one accent color, Tubi picks a *spectrum*, and the whole UI is engineered to make that spectrum sing.

The signature screen is the **Home feed**: a near-full-width featured hero at the top (a single big title with a gradient/photo backdrop, a "Tubi Original" or genre badge, title, metadata line, and a white **Play Free** button), followed by a vertical stack of **horizontally-scrolling poster rows** — "Continue Watching", "Trending Now", "Free Movies You'll Love", "Leaving Soon", genre rails, and so on. Posters are tall 2:3 cards, densely packed (3.4 visible per row on a 390pt screen), each carrying a small yellow **"FREE"** tag in the corner. This density is deliberate: Tubi's entire pitch is *catalog breadth at zero cost*, so the UI shows as many titles as possible per screen. The other signature surface is the **Title Detail** page — a full-bleed backdrop, a giant title, a prominent white Play button, "+ My List", a synopsis, cast, and "More Like This" rails — and the **Live TV guide**, an EPG-style channel grid for Tubi's free linear channels.

The atmosphere is **energetic, youthful, and unapologetically commercial**. Tubi leans into the fact that it's ad-supported rather than hiding it: the yellow "FREE" badge is a *feature*, not an apology. Motion is punchy — posters scale slightly on focus, the hero cross-dissolves between featured titles, and the gradient shimmers subtly behind the wordmark on launch. Typography is a tight geometric grotesque (Inter is the closest free analog to Tubi's custom face): heavy weights (800–900) for titles, clean 400 body, all-caps tracked labels for badges. The result reads like a neon-lit movie marquee rebuilt for a phone — bold, saturated, and proudly free.

**Key Characteristics:**
- Deep indigo-black canvas (`#0A0A2A`) — cool and slightly violet so the gradient glows; dark-only, no light mode
- Signature purple→magenta gradient (`#7408FF` → `#FF00FF`) — the brand, used on wordmark, focus, active tab, progress, secondary CTAs
- Oversized featured hero at top of Home — single title, badge, white "Play Free" button
- Dense horizontal poster rows — tall 2:3 cards, 3+ visible per row, packed for catalog-breadth feel
- Yellow `#FFD400` "FREE" tag on every poster — Tubi's promise stated as a UI feature
- White (`#FFFFFF`) primary Play button — maximum contrast against the cool dark field
- "Tubi Original" badges in gradient — signals exclusive content
- Continue-watching progress bars in gradient under each in-progress poster
- Live TV EPG guide — channel grid for Tubi's free linear channels
- Bottom tab bar: Home / Search / Live TV / My List / Account — active icon in magenta
- All-caps tracked micro-labels (badges, tags) in heavy weight; titles 800–900

## 2. Color Palette & Roles

### Primary (Brand Gradient & Interactive)
- **Tubi Purple** (`#7408FF`): Gradient start — wordmark, focus rings, active states, progress fill start.
- **Tubi Magenta** (`#FF00FF`): Gradient end — wordmark, active tab icon, progress fill end.
- **Tubi Violet** (`#A12BFF`): Mid-gradient stop / solid violet for single-color uses (chips, links on light press).
- **Tubi Pink** (`#FF4FD8`): Tappable text links, "More Like This", inline actions.
- **Play White** (`#FFFFFF`): Primary Play / Play Free button background — the highest-priority CTA.
- **Play White Pressed** (`#E6E6F2`): Pressed state of the white play button.

### Canvas & Surfaces (Dark — the only mode)
- **Canvas Indigo-Black** (`#0A0A2A`): Primary app background.
- **Surface 1** (`#15153D`): Cards, sheets, the title-detail panel, settings rows.
- **Surface 2** (`#1E1E52`): Elevated surfaces, pressed rows, search field, chips.
- **Surface 3** (`#262666`): Highest elevation — modals, popovers, focused poster lift.
- **Divider** (`#2A2A5C`): Hairline separators between sections and list rows.
- **Overlay Scrim** (`rgba(10,10,42,0.95)`): Bottom gradient over the hero backdrop for text legibility.

### Text
- **Text Primary** (`#FFFFFF`): Titles, body, primary content.
- **Text Secondary** (`#B9B9D6`): Metadata lines, synopsis, secondary labels — a cool lavender-grey.
- **Text Tertiary** (`#7A7AA0`): Disabled, timestamps, very low-emphasis captions.
- **Text On-White** (`#0A0A2A`): Text on the white Play button (the indigo-black canvas color).

### Semantic & Accent
- **Free Tag Yellow** (`#FFD400`): The "FREE" badge on posters and the hero — Tubi's signature warm accent. Text on it is `#1A0A2A`.
- **Success Green** (`#1FD17B`): "Added to My List" confirmation, download-complete.
- **Error Red** (`#FF4A6E`): Playback errors, failed sign-in, destructive confirms.
- **Live Red** (`#FF3B5C`): "LIVE" indicator dot on Live TV channels.
- **Rating Amber** (`#FFB020`): Star ratings / IMDb-style score chips.

### Gradient Tokens
| Token | Definition | Use |
|-------|-----------|-----|
| `gradient/brand` | `linear-gradient(135deg, #7408FF 0%, #FF00FF 100%)` | Wordmark, secondary CTA, active tab, "Tubi Original" badge |
| `gradient/progress` | `linear-gradient(90deg, #7408FF 0%, #FF00FF 100%)` | Continue-watching progress fill |
| `gradient/focus` | `linear-gradient(135deg, #7408FF, #FF00FF)` as 2pt ring | Focused / selected poster outline |
| `gradient/hero-scrim` | `linear-gradient(180deg, transparent 40%, rgba(10,10,42,0.95) 100%)` | Bottom fade on hero & detail backdrops |

### Note on Theme
Tubi ships **dark-only**. There is no light mode toggle; the indigo-black canvas is the brand environment. Do not invent a light palette — if a light surface is ever needed (e.g., a system share sheet), defer to the OS, not a Tubi-themed light scheme.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest free analog to Tubi's custom geometric grotesque. Tubi's brand face is a tight, slightly condensed sans with heavy display weights; Inter at weights 800–900 reproduces the marquee feel.
- **Display Alternative**: `Archivo` or `Archivo Black` (Google Fonts, SIL OFL) — for extra-punchy hero titles if a more condensed display face is wanted.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for runtime/metadata (`1h 52m`), proportional elsewhere.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Title | Inter | 32pt | 900 | 1.0 | -0.6pt | Featured title on Home hero — heaviest weight |
| Detail Title | Inter | 28pt | 800 | 1.05 | -0.5pt | Title on the Title Detail page |
| Row Header | Inter | 22pt | 800 | 1.1 | -0.3pt | "Trending Now", "Continue Watching" rail headers |
| Section Title | Inter | 18pt | 700 | 1.2 | -0.2pt | Sub-section headers, settings group titles |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Synopsis, descriptions, body copy |
| Card Title | Inter | 15pt | 600 | 1.15 | 0pt | Poster captions, list-row titles |
| Button Label | Inter | 15pt | 800 | 1.0 | 0pt | Play / Register / CTA text |
| Meta | Inter | 13pt | 500 | 1.3 | 0pt | "2024 · Thriller · 1h 52m · TV-MA" |
| Caption | Inter | 12pt | 500 | 1.3 | 0pt | Timestamps, fine print, episode counts |
| Badge / Tag | Inter | 11pt | 700 | 1.0 | 0.4pt | "TUBI ORIGINAL", "FREE", "NEW" — ALL CAPS |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Weight is the brand**: Tubi titles are *heavy* (800–900). The contrast between a 900 hero title and 400 body is the primary hierarchy signal — never set titles below 700.
- **ALL CAPS for badges only**: Genre/Original/FREE badges and tags are uppercase with positive tracking (`+0.4pt`). Body and titles are sentence case, never tracked positive.
- **Tight display tracking**: Hero and detail titles use negative letter-spacing (-0.5 to -0.6pt) for a dense, condensed marquee look.
- **Cool lavender secondary**: Metadata uses `#B9B9D6` (a cool lavender-grey), not neutral grey — it harmonizes with the indigo canvas.
- **Tabular numerals for time**: Runtimes, "min left", and clock times use tabular figures so rows don't jitter.
- **Dynamic Type**: Titles, body, synopsis scale; badge text, tab labels, and progress labels stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Play / Play Free)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FFFFFF` (Play White) — the single highest-contrast CTA
- Text + leading play glyph: `#0A0A2A` (canvas indigo-black), Inter 15pt weight 800
- Height: 48pt; padding 14pt vertical, 30pt horizontal; leading SF play.fill 13pt
- Pressed: background `#E6E6F2` + scale 0.98
- Full-width variant on Title Detail; inline width on hero

**Secondary Button (Register / Sign Up / Upgrade)**
- Background: `gradient/brand` (`linear-gradient(135deg, #7408FF, #FF00FF)`)
- Text: `#FFFFFF`, Inter 15pt weight 800
- Corner radius: 8pt; height 48pt
- Pressed: `filter: brightness(0.92)` + scale 0.98

**Tertiary Button (+ My List / Share / Download)**
- Background: `rgba(255,255,255,0.10)`
- Border: 1pt `rgba(255,255,255,0.25)`
- Text + icon: `#FFFFFF`, Inter 14pt weight 700
- Corner radius: 8pt; height 44pt; on-detail often icon-over-label stacked (icon 22pt, label 11pt)
- Pressed: background `rgba(255,255,255,0.18)`

**Text Button (More Like This / See All / Cast & Crew)**
- Font: Inter 14pt weight 700
- Color: `#FF4FD8` (Tubi Pink)
- No background, no underline
- Pressed: opacity 0.7

**Icon Button (top bar: download, search, profile)**
- 24pt glyph in 44pt hit area
- Default `#FFFFFF`; no background
- Active/selected tints to magenta `#FF00FF`

### Core Atoms

**Poster Card (the core content unit)**
- Aspect ratio: 2:3 (e.g., 100×150pt on a 390pt screen, ~3.4 visible per row)
- Corner radius: 10pt
- Shadow: `0 6px 16px rgba(0,0,0,0.45)`
- Leading-top **FREE tag**: `#FFD400` bg, `#1A0A2A` text, 8pt, weight 900, 2×5pt padding, 3pt radius
- Optional bottom caption overlay: title 11pt weight 700 `#FFFFFF` with `text-shadow: 0 1px 4px rgba(0,0,0,0.8)`
- In-progress variant: 4pt `gradient/progress` bar pinned to the card's bottom edge
- Focused/selected: 2pt `gradient/focus` ring + scale 1.04 (150ms)

**Featured Hero**
- Height: ~230–300pt (≈ 60% of viewport width tall)
- Full-bleed backdrop image/gradient, corner radius 18pt (inset 14pt from screen edges) or full-bleed on Detail
- `gradient/hero-scrim` bottom fade for text legibility
- Top-left badge: "TUBI ORIGINAL" / genre — `gradient/brand` pill, 9pt weight 800 caps
- Stacked: title (32pt/900) → meta line (11pt/500 `#B9B9D6`) → button row (white Play Free + circular + add)
- Auto cross-dissolves between 4–6 featured titles every ~6s (400ms fade)

**Row Header**
- Title 22pt weight 800; optional trailing "See All ›" in `#FF4FD8` 13pt weight 700
- 16pt horizontal inset, 10pt bottom gap before the poster strip

**Free Tag / Badge Chip**
- "FREE": `#FFD400` fill, `#1A0A2A` text — the signature
- "TUBI ORIGINAL": `gradient/brand` fill, white text
- "NEW" / "LEAVING SOON": `#1E1E52` fill, `#FF4FD8` text
- All: 11pt weight 700 caps, +0.4pt tracking, 3pt radius, 2×6pt padding

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe-area home indicator
- Background: `rgba(10,10,42,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A2A5C`
- Tabs (5): Home, Search, Live TV, My List, Account
- Icon size: 22pt; label Inter 10pt weight 600
- Active: icon + label in `#FF00FF` (magenta), icon filled
- Inactive: `#7A7AA0`
- No pill indicator — color + fill change only

**Top Bar (Home)**
- Height: 44pt + safe area
- Leading: `tubi` gradient wordmark (≈22pt cap height)
- Trailing: download, search, profile icons (24pt, `#FFFFFF`, 16pt gap)
- Transparent over hero; gains `#0A0A2A` background after scroll-past-hero

**Title Detail Nav**
- Transparent over backdrop; back chevron top-left (`#FFFFFF`, 24pt, in a 32pt circular `rgba(0,0,0,0.4)` scrim)
- Trailing: cast / share icons

### Input Fields

**Search Bar**
- Height: 44pt
- Background: `#1E1E52`
- Corner radius: 10pt
- Leading magnifyingglass 16pt `#7A7AA0`
- Placeholder "Search movies, shows, channels" 14pt `#7A7AA0`
- Text: `#FFFFFF` 15pt
- Focus: 1.5pt `#7408FF` border

**Search Results Grid**
- 3-column poster grid, 10pt gutter, 16pt screen inset, FREE tag on each

### Distinctive Components

**Live TV EPG Guide**
- Vertical list of channel rows; each row: leading 56pt channel logo tile (`#15153D` bg, 10pt radius) + channel name (15pt/600) + a horizontally-scrolling timeline of program blocks
- Program block: `#1E1E52` fill, 10pt radius, 8pt padding, program title 13pt/600 + time range 11pt/500 `#B9B9D6`
- "On Now" block: `gradient/brand` left border (3pt) + faint gradient wash; leading `#FF3B5C` "LIVE" dot
- Sticky time-header strip across the top (every 30 min), `#7A7AA0` 11pt
- Tapping "On Now" → full-screen player; tapping future block → reminder/detail sheet

**Continue-Watching Card**
- Poster with a 4pt `gradient/progress` bar at the bottom edge (width = % watched)
- Below caption: "41 min left" or "S2:E4" in 12pt `#B9B9D6`
- Long-press → "Remove from Continue Watching" context sheet

**Tubi Original Badge**
- Pill: `gradient/brand` fill, white "TUBI ORIGINAL" text 9pt weight 800 caps, top-left of hero/poster

**Ad Marker (player)**
- During free playback, a thin `#FFD400` segment-marked scrubber shows ad-break positions
- "Ad · 0:15" chip top-left during an ad in `rgba(0,0,0,0.6)` with `#FFD400` accent

**Genre Rail Pills (browse)**
- Horizontal scroller of pills: `#1E1E52` bg, `#FFFFFF` 14pt/600, 10pt radius; selected → `gradient/brand` fill

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 16pt
- Poster gutter: 10pt
- Row vertical rhythm: 16pt between a row's last poster and the next row header
- Hero inset from screen edges: 14pt (rounded 18pt) on Home; full-bleed on Detail

### Grid & Container
- iPhone: 16pt insets; poster rows scroll edge-to-edge (first poster aligned to 16pt inset, content bleeds past right edge)
- Poster row: ~3.4 cards visible at 390pt width
- Search / browse grid: 3 columns, 10pt gutter
- iPad: 5–6 column poster grids; hero spans full width with larger 36–40pt title
- Live TV guide: fixed-width channel-label column (96pt) + horizontally-scrolling program timeline

### Whitespace Philosophy
- **Density over air**: Tubi intentionally packs the screen — many posters per row, tight 10pt gutters — to communicate catalog breadth ("thousands of free titles"). This is the opposite of a minimalist content app.
- **Hero breathes, rows pack**: the featured hero gets generous height and a clear scrim; the rows beneath are deliberately dense.
- **Edge-bleeding rows**: poster strips run off the right edge to signal "there's more, keep scrolling".
- **Badges never crowd the title**: the FREE tag sits in the poster's top-left corner away from the bottom caption.

### Border Radius Scale
- Square (0pt): full-bleed hero/backdrop on Detail, dividers
- Subtle (4pt): progress bars, ad markers, tiny tags
- Standard (8pt): buttons, search bar
- Card (10pt): posters, channel tiles, program blocks, genre pills
- Hero (18pt): inset featured hero on Home
- Sheet (20pt): bottom sheets, modals (top corners)
- Pill (500pt): badge chips, filter pills
- Circle (50%): profile avatars, circular hero add/secondary buttons

## 6. Depth & Elevation

Tubi uses **glow and gradient** more than shadow. On the dark indigo canvas, elevation is conveyed by surface lightening (`#15153D` → `#1E1E52` → `#262666`) plus a soft black drop shadow on floating content (posters, sheets). The brand gradient itself acts as a "spotlight" cue on focus.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, on `#0A0A2A` | Page background, row headers, body text |
| Raised (Level 1) | Surface `#15153D`, `0 6px 16px rgba(0,0,0,0.45)` | Poster cards, channel tiles, list rows |
| Floating (Level 2) | Surface `#1E1E52`, `0 12px 28px rgba(0,0,0,0.55)` | Context menus, dropdowns, focused poster lift |
| Sheet (Level 3) | Surface `#15153D`, `0 -10px 40px rgba(0,0,0,0.6)` | Bottom sheets (My List actions, episode picker) |
| Modal Overlay | Scrim `rgba(5,5,16,0.7)` | Dim behind modals and sheets |
| Focus Glow | 2pt `gradient/focus` ring + scale 1.04 | Selected poster / focused interactive element |

**Shadow Philosophy**: Shadows are deep (0.45–0.6 opacity) so they register against the dark canvas, but they're never the brand statement — the *gradient glow* is. A focused poster doesn't just shadow; it gains a purple→magenta ring and scales up, as if lit by the brand.

### Motion
- **Hero cross-dissolve**: featured titles auto-rotate every ~6s with a 400ms opacity cross-fade; backdrop ken-burns drift (very slow scale 1.0→1.05 over the dwell)
- **Poster focus/press**: scale 1.0 → 1.04 over 150ms ease-out + gradient ring fades in; release reverses in 120ms
- **Row scroll**: native momentum horizontal scroll; deceleration standard iOS
- **Wordmark shimmer**: on app launch / Home appear, a subtle highlight sweeps across the `tubi` gradient wordmark over 800ms (once)
- **Play transition**: tapping Play scales the poster/hero up and cross-fades into the player over 350ms; player chrome fades out after 3s idle
- **Tab switch**: instant content swap; active icon morphs color to magenta + a 200ms fill animation
- **Bottom sheet**: slides up 300ms ease-out, dims background to `rgba(5,5,16,0.7)`; dismiss drag 1:1, threshold 40%
- **Add to My List**: + icon morphs to ✓ with a 250ms spring + brief `#1FD17B` flash + soft haptic
- **Progress bar**: continue-watching fill animates from 0 → current % over 500ms on first appear
- **Haptics**: soft impact on Play, add-to-list confirm, and tab switch; selection tick on focus change in Live TV guide

## 7. Do's and Don'ts

### Do
- Use the deep indigo-black canvas (`#0A0A2A`) — cool and slightly violet, NOT neutral black
- Make the purple→magenta gradient (`#7408FF` → `#FF00FF`) the brand signature — wordmark, focus, active tab, progress, secondary CTA
- Keep the primary Play button pure white (`#FFFFFF`) with indigo-black text — it must out-contrast everything
- Put the yellow `#FFD400` "FREE" tag on posters — it's a feature, lean into "free"
- Pack poster rows densely (3+ visible, 10pt gutters) — catalog breadth is the pitch
- Use heavy title weights (800–900) with tight negative tracking — marquee feel
- Bleed poster rows off the right edge — signals "keep scrolling, there's more"
- Use ALL-CAPS tracked labels for badges/tags only (FREE, TUBI ORIGINAL, NEW)
- Render the Live TV EPG with sticky time header + gradient-marked "On Now" blocks
- Give focused posters a gradient ring + 1.04 scale (lit-by-brand)
- Use cool lavender-grey (`#B9B9D6`) for metadata, not neutral grey

### Don't
- Don't add a light mode — Tubi is dark-only; defer to OS for system sheets
- Don't use a neutral or warm dark canvas — the indigo-violet base is what makes the gradient glow
- Don't replace the gradient with a single flat purple for brand moments — the spectrum *is* the identity
- Don't hide or shrink the "FREE" tag — it's core messaging, never an apology
- Don't make poster rows sparse/airy — density communicates the value prop
- Don't set titles below 700 weight — heavy display weight is the brand
- Don't tint the primary Play button — it stays white for max contrast
- Don't use the gradient as body text or large fills behind reading copy — it's for marks, accents, focus
- Don't add positive tracking to titles/body — only badges are tracked/uppercased
- Don't use light shadows — they vanish on the dark canvas; go deep (0.45–0.6) or use the gradient glow
- Don't animate aggressively in content rows — motion is punchy but brief (120–400ms)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ~210pt tall; ~3.2 posters/row; tab labels stay 10pt |
| iPhone 13/14/15 | 390pt | Standard layout; hero ~230pt; ~3.4 posters/row |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; top bar respects it |
| iPhone 15/16 Pro Max | 430pt | Hero ~260pt; ~3.8 posters/row; larger hero title (34pt) |
| iPad (portrait) | 768pt | Full-width hero (title 36pt); 5-column poster grids; 2-up rows |
| iPad (landscape) | 1024pt+ | Cinematic full-width hero (title 40pt); 6-column grids; Live TV guide shows more channels |

### Dynamic Type
- Hero/detail titles, row headers, body, synopsis: full scale
- Badge text, tab labels, progress labels, ad-marker chip, EPG time header: FIXED (layout-sensitive)
- Poster captions: scale to L only, then truncate with ellipsis

### Orientation
- Phone: portrait-locked for browse; player rotates to landscape (and supports fullscreen landscape)
- iPad: all orientations; landscape gets the cinematic wide hero
- Live TV player: landscape fullscreen with overlaid guide drawer

### Touch Targets
- Poster card: full card tappable (min 100×150pt)
- Tab bar icons: 22pt glyph, 56pt row height, full-segment tap
- Play button: 48pt tall
- Top-bar icons: 24pt glyph, 44pt hit
- EPG program block: min 44pt tall, full block tappable

### Safe Area Handling
- Top: top bar + hero respect safe area and Dynamic Island; backdrop bleeds *under* the status bar
- Bottom: tab bar respects home indicator; player chrome insets controls above it
- Sides: 16pt content inset; hero backdrop bleeds to screen edges (Detail) or 14pt inset (Home)
- Player: controls avoid notch in landscape

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (indigo-black): `#0A0A2A`
- Surface 1 / 2 / 3: `#15153D` / `#1E1E52` / `#262666`
- Divider: `#2A2A5C`
- Gradient start (Tubi Purple): `#7408FF`
- Gradient end (Tubi Magenta): `#FF00FF`
- Brand gradient: `linear-gradient(135deg, #7408FF, #FF00FF)`
- Tubi Pink (links): `#FF4FD8`
- Play button: `#FFFFFF` / text `#0A0A2A`
- FREE tag: `#FFD400` / text `#1A0A2A`
- Text primary / secondary / tertiary: `#FFFFFF` / `#B9B9D6` / `#7A7AA0`
- Success / Error / Live: `#1FD17B` / `#FF4A6E` / `#FF3B5C`

### Example Component Prompts
- "Build the Tubi Home featured hero in SwiftUI: a 230pt-tall card inset 14pt from screen edges, 18pt corner radius, full-bleed backdrop with a `linear-gradient(180deg, transparent 40%, rgba(10,10,42,0.95) 100%)` scrim. Top-left a 'TUBI ORIGINAL' pill with `linear-gradient(135deg,#7408FF,#FF00FF)` fill, white Inter 9pt weight 800 ALL CAPS +0.4pt tracking. Bottom-left stacked: title Inter 32pt weight 900 white -0.6pt tracking, meta line Inter 11pt weight 500 `#B9B9D6`, then a button row: white 'Play Free' button (48pt tall, 8pt radius, `#0A0A2A` text + leading play.fill) and a 44×38pt circular `rgba(255,255,255,0.16)` '+' button."

- "Create a Tubi poster card: 100×150pt (2:3), 10pt corner radius, `0 6px 16px rgba(0,0,0,0.45)` shadow. Top-left a 'FREE' tag — `#FFD400` background, `#1A0A2A` text, Inter 8pt weight 900, 2×5pt padding, 3pt radius. Optional bottom caption: title Inter 11pt weight 700 white with `text-shadow: 0 1px 4px rgba(0,0,0,0.8)`. On focus: 2pt `linear-gradient(135deg,#7408FF,#FF00FF)` ring + scale 1.04 over 150ms."

- "Render a Tubi content row: header 'Trending Now' in Inter 22pt weight 800 white -0.3pt tracking at 16pt leading inset, optional trailing 'See All ›' in `#FF4FD8` 13pt weight 700. Below: a horizontal scroll of 2:3 poster cards, 10pt gutter, first card at 16pt inset, content bleeding past the right screen edge. 16pt vertical gap to the next row."

- "Build a Tubi continue-watching card: standard poster with a 4pt-tall progress bar pinned to the bottom edge — `linear-gradient(90deg,#7408FF,#FF00FF)` fill at 62% width on a `rgba(255,255,255,0.16)` track. Below the card: '41 min left' in Inter 12pt weight 500 `#B9B9D6`. Animate the fill 0 → 62% over 500ms on first appear."

- "Render the Tubi Live TV EPG guide: a vertical list of channel rows. Each row: a 56pt square channel logo tile (`#15153D`, 10pt radius) + channel name Inter 15pt weight 600 white, then a horizontally-scrolling timeline of program blocks. Each block: `#1E1E52` fill, 10pt radius, 8pt padding, title Inter 13pt weight 600 + time range Inter 11pt weight 500 `#B9B9D6`. The 'On Now' block gets a 3pt `linear-gradient(135deg,#7408FF,#FF00FF)` left border, a faint gradient wash, and a leading `#FF3B5C` 'LIVE' dot. Sticky 30-min time header strip on top in `#7A7AA0` 11pt."

- "Build the Tubi bottom tab bar: 56pt tall + safe area, `rgba(10,10,42,0.96)` background with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A2A5C`. Five tabs — Home, Search, Live TV, My List, Account — 22pt icons, Inter 10pt weight 600 labels. Active tab icon + label `#FF00FF` (magenta) with the icon filled; inactive `#7A7AA0`. No pill indicator."

### Iteration Guide
1. Canvas is deep indigo-black `#0A0A2A` (cool, slightly violet) — NOT neutral black, NOT charcoal; dark-only
2. The purple→magenta gradient (`#7408FF` → `#FF00FF`) is THE brand — wordmark, focus, active tab, progress, secondary CTA
3. Primary Play button is pure white `#FFFFFF` with `#0A0A2A` text — highest contrast, never tinted
4. Every poster carries a yellow `#FFD400` "FREE" tag — it's a feature, never hidden
5. Poster rows are dense (3+ visible, 10pt gutters) and bleed off the right edge — catalog breadth is the pitch
6. Titles are heavy (800–900) with tight negative tracking; only badges are ALL-CAPS + positive-tracked
7. Metadata uses cool lavender-grey `#B9B9D6`, not neutral grey
8. Elevation = surface lightening + deep shadow (0.45–0.6); focus = gradient ring + 1.04 scale (lit-by-brand)
9. Live TV is an EPG channel grid with a sticky time header and gradient-marked "On Now" blocks
10. Bottom tabs: Home / Search / Live TV / My List / Account — active icon magenta `#FF00FF`, no pill
