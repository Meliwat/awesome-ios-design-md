# Design System Inspiration of Paramount+ (iOS)

## 1. Visual Theme & Atmosphere

Paramount+'s iOS app is a network-bundle streaming surface built on a deep, confident navy. It is dark-only — there is no light mode — anchored on a midnight-navy canvas (`#0A0E2D`) that subtly lightens to `#0E1438` beneath hero artwork. The navy is a deliberate brand choice: it echoes the iconic Paramount blue mountain and reads as broadcast-prestige rather than the flat black of generic streamers. Open the Home tab and the first thing you see, directly under a thin top bar carrying the mountain logomark and "Paramount+" wordmark (the "+" rendered in brand blue), is the **brand-hubs row** — a horizontal strip of network-colored chips (CBS, Paramount Network, MTV, Nickelodeon, Comedy Central, Showtime). This row is the signature framing device: it immediately tells the user "this is a bundle of the networks you know," and tapping a hub re-skins the whole browse experience into that network. Below the hubs sits a full-bleed **hero billboard** for a featured Original, followed by horizontally scrolling content rows — **Live TV & Sports**, **Trending**, **Keep Watching**, **Because you watched** — of 2:3 poster cards.

The accent system is monochrome-on-navy with a single brand color doing all the work: **Paramount+ Blue (`#0064FF`)**. It fills every primary CTA ("Play"), tints the active tab, draws progress bars, and provides selection rings. There is no second accent — a brighter sky-blue (`#4D9DFF`) is used only for the active-tab tint and eyebrow labels for legibility, and a single broadcast red (`#FF2D46`) flags Live TV and sports (a core Paramount+ pillar covering NFL on CBS, UEFA Champions League, the Masters, SEC football, WWE). Everything else is white / `#AEB4D6` text on navy, so full-color poster art is the loudest thing on screen.

The defining product moment is the **hub + hero pairing**. Where Netflix opens straight to a billboard, Paramount+ leads with the hubs row *then* the hero — establishing the network-bundle identity before the featured title. The hero itself uses a heavy treatment: a blue uppercase eyebrow ("PARAMOUNT+ ORIGINAL"), an ultra-bold title (Inter weight 900), a dot-separated metadata line, and a blue "Play" + glass "+ My List" button pair.

Typography is Paramount Sans (a proprietary grotesque); the closest free substitute is **Inter**. The hierarchy is broadcast-bold: hero titles at weight 900, row headers at 22pt weight 700, all-caps tracked eyebrows at 11pt weight 800. The system is built to read cleanly across the matching tvOS, web, and mobile apps.

**Key Characteristics:**
- Dark-only — midnight-navy canvas (`#0A0E2D`) lightening to `#0E1438` under hero art; no light mode
- **Brand-hubs row** — network-colored chips (CBS, MTV, Nickelodeon, Comedy Central, Showtime) high on Home; the bundle identity
- Full-bleed **hero billboard** for a featured Original, directly below the hubs row
- **Single accent** — Paramount+ Blue (`#0064FF`) for every primary CTA, progress, selection; sky-blue (`#4D9DFF`) for active-tab tint
- **Live TV & Sports** flagged with red (`#FF2D46`) + a pulsing dot — a first-class Paramount+ pillar
- **Content rows** — horizontally scrolling 2:3 poster cards; Live cards carry a red LIVE flag
- Broadcast-bold typography — Inter (Paramount Sans fallback), hero weight 900, 11pt uppercase tracked eyebrows
- Monochrome navy chrome — white / `#AEB4D6` text so full-color art leads
- Bottom tab bar: Home / Live TV / Search / My List — sky-blue active tint
- Mountain logomark + "Paramount**+**" wordmark with the blue "+" — the brand signature

## 2. Color Palette & Roles

Paramount+ ships dark-only. The "Light Mode" column documents the rare light surfaces (account/legal web views) for completeness; the product UI uses the dark column everywhere.

### Primary (Interactive)
- **Paramount+ Blue** (`#0064FF`): Primary action — "Play" / "Resume" / "Watch Live" fill; progress fill; selection ring. The one brand accent.
- **Blue Pressed** (`#0052CC`): Pressed state + scale 0.98.
- **Sky Accent** (`#4D9DFF`): Active-tab tint, eyebrow labels (brighter for contrast on navy).
- **Glass Fill** (`rgba(255,255,255,0.14)`): Secondary action ("+ My List", "More Info") — translucent over art.

### Canvas & Surfaces (Light Mode — account / web views only)
- **Light Canvas** (`#FFFFFF`): Legal & web-account pages.
- **Light Surface** (`#F2F4FB`): Cards in account views.
- **Light Divider** (`#DDE1F0`): Hairlines in light web views.
- **Light Text** (`#0A0E2D`): Body text in light web views.

### Canvas & Surfaces (Dark Mode — the product)
- **Canvas** (`#0A0E2D`): Primary app background — midnight navy.
- **Canvas 2** (`#0E1438`): Background under hero billboard; subtle lift.
- **Surface 1** (`#161C44`): Cards, sheets, settings rows.
- **Surface 2** (`#1F2655`): Elevated chips, progress track, pressed rows.
- **Divider** (`#2A3266`): Hairline dividers, tab-bar top border.

### Text
- **Text Primary** (`#FFFFFF`): Titles, hero, primary labels.
- **Text Secondary** (`#AEB4D6`): Metadata, captions, "See All".
- **Text Tertiary** (`#6F77A6`): Inactive tabs, disabled, faint meta dots.

### Semantic
- **Live Red** (`#FF2D46`): Live TV / sports badge, "LIVE" flag — pulsing dot.
- **Success Green** (`#1ED760`): Download-complete, save confirmation toast.
- **Error Red** (`#FF453A`): Playback error, payment failure.
- **Rating Gold** (`#F5C518`): Critic / star rating.

### Role → Color Table

| Role | Light Mode | Dark Mode (product) |
|------|-----------|---------------------|
| Canvas | `#FFFFFF` | `#0A0E2D` |
| Canvas (hero) | `#FFFFFF` | `#0E1438` |
| Surface 1 | `#F2F4FB` | `#161C44` |
| Surface 2 | `#E8EBF7` | `#1F2655` |
| Divider | `#DDE1F0` | `#2A3266` |
| Text Primary | `#0A0E2D` | `#FFFFFF` |
| Text Secondary | `#5A6088` | `#AEB4D6` |
| Text Tertiary | `#9298BE` | `#6F77A6` |
| Primary CTA | `#0064FF` | `#0064FF` |
| CTA Label | `#FFFFFF` | `#FFFFFF` |
| Active Tab | `#0064FF` | `#4D9DFF` |
| Live | `#FF2D46` | `#FF2D46` |

## 3. Typography Rules

### Font Family
- **Brand**: `Paramount Sans` — a proprietary grotesque commissioned for the Paramount+ identity.
- **Closest Free Substitute**: `Inter` (by Rasmus Andersson, Google Fonts, SIL OFL) — a neutral grotesque with excellent hinting and a wide weight range up to 900.
- **Alternate Substitute**: `Manrope` or `Archivo` if Inter is unavailable.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Lining, tabular for time codes and live scores; proportional elsewhere.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 900 | 1.05 | -0.6pt | "Home", "My List" nav titles |
| Hero Title | Inter | 26pt | 800 | 1.02 | -0.5pt | Featured billboard title (logo lockup may replace) |
| Row Header | Inter | 22pt | 700 | 1.15 | -0.2pt | "Live TV & Sports", "Trending" |
| Card Title | Inter | 18pt | 700 | 1.2 | -0.1pt | Detail-page / large-card title |
| Body | Inter | 16pt | 600 | 1.5 | 0pt | Synopsis, descriptions |
| Body Regular | Inter | 16pt | 400 | 1.5 | 0pt | Long-form legal / settings copy |
| Caption | Inter | 15pt | 500 | 1.35 | 0pt | "S2 · E5 · 52 min" episode meta |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | "2024 · Crime Drama · TV-MA" dot-separated |
| Poster Caption | Inter | 11pt | 500 | 1.25 | 0pt | Title under a poster card |
| Tab Label | Inter | 12pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Eyebrow | Inter | 11pt | 800 | 1.0 | 1.6pt (uppercase) | "PARAMOUNT+ ORIGINAL" over hero |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | Primary/secondary button label |
| Hub Label | Inter | 11pt | 800 | 1.0 | 0.3pt | Network name on a hub chip |
| Badge | Inter | 8pt | 800 | 1.0 | 0.5pt (uppercase) | "LIVE", "NEW" |

### Principles
- **Broadcast boldness**: Hero and screen titles run heavy (800-900) — Paramount+ reads like prestige-network branding, not a utility app.
- **All-caps tracked eyebrows**: Category/Original labels above the hero are 11pt uppercase weight 800 with +1.6pt tracking — the signature framing device.
- **Hub chips speak in network voice**: hub labels use weight 800 in each network's brand color so CBS / MTV / Showtime read instantly.
- **Monochrome chrome, color content**: chrome type is white / `#AEB4D6` only; the single blue accent never sits behind body text.
- **Dynamic Type**: Titles, body, captions scale; tab labels, eyebrows, hub labels, badges stay fixed (layout-sensitive over art).

## 4. Component Stylings

### Buttons

**Primary Button (Play / Resume / Watch Live)**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#0064FF` (Paramount+ Blue — the one accent)
- Text/Icon: `#FFFFFF`, Inter 15pt weight 700, leading ▶ play glyph 14pt
- Padding: 13pt vertical, 28pt horizontal
- Pressed: background `#0052CC` + scale 0.98
- Width: hugs content on hero; full-width on detail page

**Secondary / Glass Button (+ My List / More Info / Trailer)**
- Background: `rgba(255,255,255,0.14)` with `backdrop-filter: blur(6px)`
- Text: `#FFFFFF`, Inter 14pt weight 600
- Border: none
- Corner radius: 6pt
- Padding: 12pt vertical, 16-22pt horizontal
- Pressed: background `rgba(255,255,255,0.22)`

**Outline Button (Watch Live secondary / Schedule)**
- Background: transparent
- Border: 1.5pt `rgba(255,255,255,0.4)`
- Text: `#FFFFFF`, Inter 14pt weight 600
- Corner radius: 6pt
- Padding: 11pt vertical, 20pt horizontal

**Text Button (More Info / See All / Manage)**
- Background: transparent
- Text: `#AEB4D6`, Inter 14pt weight 600
- No underline
- Pressed: text → `#FFFFFF`

**Icon Button (search, profile, add)**
- 21pt glyph, 44pt hit area
- Color `#FFFFFF`; inactive `#6F77A6`
- No background

### Core Atoms

**Poster Card**
- 2:3 aspect ratio, ~116pt wide on phone (variable per row)
- Corner radius: 8pt
- Full-bleed key art, no border
- Optional red "LIVE" flag top-left with a 5pt pulsing white dot, Inter 8pt w800
- Optional blue "NEW" badge top-left, Inter 8pt w800
- Caption below: title, Inter 11pt w500 `#AEB4D6`
- Pressed: scale 0.97, 150ms

**Hub Chip (Brand-hubs row)**
- ~70pt min-width × 40pt rounded rectangle, 8pt corner radius
- Filled with the network's brand gradient (CBS blue, Paramount black, MTV red, Nick orange, Comedy Central purple, Showtime green)
- Network name centered, Inter 11pt w800, white (or near-black on light networks like Nick)
- Pressed: scale 0.96; tapping re-skins browse into that network

**Hero Billboard**
- Full-bleed key art occupying ~46% of screen height (332pt on phone), directly below the hubs row
- Bottom gradient scrim: `linear-gradient(180deg, transparent 36%, rgba(10,14,45,0.72) 70%, #0A0E2D 100%)`
- Left-aligned: eyebrow (11pt uppercase sky-blue) → title or logo lockup → meta row (`12pt #AEB4D6` dot-separated) → button pair (blue Play + glass +My List)

**Live Badge**
- `#FF2D46` fill, 4pt corner radius, Inter 11pt w800 uppercase, 5pt/10pt padding
- Leading 7pt white dot that pulses (opacity 1 → 0.4, 1.2s loop)

**Progress Bar (Keep Watching)**
- 4pt track height, 2pt radius, track `#1F2655`
- Fill: `#0064FF` solid
- Label below: Inter 11pt w500 `#AEB4D6` — "S2 E5 · 22 min left"

**Live Score Strip (sports cards)**
- Compact row over live event art: team marks + score in tabular Inter 13pt w700, red "LIVE" flag

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(10,14,45,0.96)` with `backdrop-filter: blur(18px) saturate(160%)`
- Top border: 0.5pt `#2A3266`
- Tabs (4): Home, Live TV, Search, My List
- Icon: 22pt; active = `#4D9DFF` **filled** glyph, inactive = `#6F77A6` stroked glyph
- No tint pill — the color + fill swap is the active treatment
- Labels: Inter 12pt w600, always shown

**Top Bar (Home)**
- Height: 44pt + safe area
- Leading: mountain logomark (22pt) + "Paramount" wordmark + blue "+" (Inter 19pt w800)
- Trailing: search glyph + profile/add (21pt, white)
- Solid `#0A0E2D`; hubs row pinned directly below

**Detail Page**
- Full-bleed hero art + back chevron (top-left, white, on 40%-black circle)
- Sticky title bar appears on scroll past hero title

### Input Fields

**Search Bar**
- Height: 44pt
- Background: `#161C44`
- Border: none (1.5pt `#0064FF` on focus)
- Corner radius: 8pt
- Leading magnifier 18pt `#6F77A6`
- Placeholder: "Search shows, movies, sports" Inter 14pt `#6F77A6`
- Text: white

**Profile / PIN Entry**
- Segmented digit boxes, `#161C44` fill, 8pt radius, white digits, focus ring `#0064FF`

### Distinctive Components

**Brand-Hubs Row**
- Horizontal strip of network-brand chips directly under the top bar — the bundle identity
- Tapping a hub re-skins the Home browse into that network's curated content
- Paramount+'s most distinctive structural element

**Mountain Logomark + "Paramount+" Wordmark**
- Blue mountain peak with radiating stars + "Paramount" in white and "+" in `#0064FF`
- The brand signature; top bar, splash, hero Original lockups

**Hero Billboard (below hubs)**
- Featured Original with a blue uppercase eyebrow and weight-900 title — the prestige moment

**Live TV & Sports Lane**
- A first-class row of live event cards with red LIVE flags and live score strips — NFL on CBS / UEFA / SEC / WWE / CBS News 24/7

**Keep Watching Rail**
- Resume cards with a blue progress bar and "S2 E5 · 22 min left" caption

**Network Hub Page**
- Tapping a hub opens a fully re-skinned browse surface in that network's brand color — CBS / MTV / Nickelodeon / Comedy Central / Showtime

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48, 64
- Content horizontal inset: 18pt (rows bleed to the right edge for "more content" affordance)
- Inter-row vertical gap: 12-16pt
- Poster gap within a row: 10pt; hub chip gap: 8pt

### Grid & Container
- iPhone: 18pt side inset; rows scroll horizontally and bleed off the right edge
- iPad: rows show more posters per viewport; hero billboard taller (440pt); hubs row larger chips
- Hero billboard: ~46% of screen height on phone, full-bleed, directly below the hubs row
- No max content width — rows are edge-to-edge horizontal scrollers

### Whitespace Philosophy
- **Bundle-first**: the hubs row gets pinned, breathable space at the very top — it is the structural anchor
- **Catalog density**: rows are packed tight (10pt gaps) so more art is visible — a content-discovery surface
- **Chrome recedes**: navy canvas frames full-color art with no competing color; only blue accents punctuate

### Border Radius Scale
- Square (0pt): full-bleed hero art, scrims
- Small (4pt): badges, live flag, progress track
- Standard (6pt): buttons, search results
- Card (8pt): poster cards, hub chips, search bar, settings rows
- Comfortable (12pt): inline cards, tooltips
- Sheet (20pt): bottom sheets, modals
- Pill (500pt): filter chips, toggle
- Circle (50%): avatars, back-button background

## 6. Depth & Elevation

Paramount+ uses depth sparingly — the subtle navy lift under hero art is the primary depth cue, not heavy drop shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Rows, posters, hubs, tabs on the navy canvas |
| Hero Lift | `#0E1438` bleed behind art + bottom scrim | Featured billboard depth |
| Card Lift (Level 1) | `rgba(0,0,0,0.45) 0 8px 22px` | Focused/hovered poster (tvOS parity); pressed scale 0.97 |
| Floating (Level 2) | `rgba(0,0,0,0.55) 0 12px 30px` + 1pt `#2A3266` border | Bottom sheets, context menus, "Add to" sheet |
| Modal Overlay | `rgba(0,0,0,0.6)` | Dim behind sheets, age-gate, plan picker |

**Shadow Philosophy**: On a deep navy canvas, soft shadows barely register, so Paramount+ leans on (a) the subtle `#0E1438` lift under hero art and (b) a 1pt `#2A3266` border on floating panels as the elevation cue. Posters lift only via scale on press/focus. Nothing in the resting catalog casts a visible shadow — full-color art against muted navy provides all the figure/ground separation needed.

### Motion
- **Hub re-skin**: tapping a hub cross-fades the whole browse into the network's brand color over 300ms ease-in-out
- **Hero auto-rotate / parallax**: the featured billboard may cross-fade between titles every ~8s (400ms ease-in-out); art parallaxes subtly on scroll
- **Row scroll**: native momentum horizontal scroll; cards do not snap (free scroll), 60fps
- **Poster press**: scale 1.0 → 0.97 in 120ms ease-out; release springs back
- **Tab switch**: instant content swap; icon color+fill cross-fades 150ms
- **Detail open**: poster expands / pushes into full-bleed detail with 300ms ease-out shared-element-style transition
- **Live dot pulse**: opacity 1 → 0.4 → 1 over 1.2s, infinite ease-in-out
- **Sheet present**: bottom sheet slides up 300ms ease-out; dim fades in parallel
- **Progress fill**: animates from 0 to value over 400ms when a Keep Watching row appears
- **Haptic feedback**: light impact on poster select, soft on hub switch and add-to-My-List confirm

## 7. Do's and Don'ts

### Do
- Keep the app dark-only on midnight navy `#0A0E2D`; lift to `#0E1438` under hero art
- Lead Home with the brand-hubs row, then the hero — the bundle identity comes first
- Use Paramount+ Blue `#0064FF` as the single accent for every primary CTA, progress, selection
- Use sky-blue `#4D9DFF` for the active-tab tint and eyebrow labels (legibility on navy)
- Flag Live TV & sports with red `#FF2D46` + a pulsing dot — it is a first-class pillar
- Render the hero with an 11pt uppercase tracked sky-blue eyebrow and a weight-900 title
- Color hub chips in each network's brand color (CBS / MTV / Nick / Comedy Central / Showtime)
- Use heavy Inter weights (700-900) for hero & screen titles — broadcast prestige
- Let full-color poster art be the loudest thing on screen; keep chrome monochrome navy
- Re-skin the entire browse surface into a network's brand color when its hub is tapped

### Don't
- Don't add a light mode — Paramount+ is dark-only
- Don't use pure `#000000` for the canvas — the navy `#0A0E2D` / `#0E1438` is the signature
- Don't introduce a second brand accent — Paramount+ Blue + live-red is the entire chromatic budget
- Don't skip the hubs row or push it below the hero — it is the structural anchor of the bundle identity
- Don't use the blue accent as a fill behind body text or as a poster overlay
- Don't put borders on poster cards — art is full-bleed; separation comes from the navy canvas
- Don't animate aggressively — motion is prestige-quiet (120-400ms ease-out)
- Don't use a Material-style tint pill on the active tab — the color+fill swap is the treatment
- Don't snap-scroll content rows — they are free momentum scrollers that bleed off the right edge
- Don't center body/synopsis text — only hub chips are centered; hero & rows are left-aligned
- Don't let chrome compete with art — chrome stays white / `#AEB4D6` on navy

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ~300pt; hubs row scrolls; ~2.4 posters per viewport |
| iPhone 13/14/15 | 390pt | Standard layout; hero 332pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in solid top bar |
| iPhone 15/16 Pro Max | 430pt | Hero 372pt; ~3 posters per viewport |
| iPad (portrait) | 768pt | Hero 440pt full-bleed; ~5 posters per row; larger hub chips; 24pt insets |
| iPad (landscape) | 1024pt+ | Hero 520pt; ~7 posters per row; hubs row spans wider |

### Dynamic Type
- Hero title, screen/row headers, body, captions: full scale
- Tab labels, eyebrows, hub labels, badges, "LIVE": FIXED (composited over art / layout-sensitive)
- Synopsis on detail page scales to XL

### Orientation
- Phone: portrait-primary for browsing; player rotates to landscape full-screen
- iPad: both orientations for browse; player always landscape full-screen
- Hero art re-crops (focal point preserved) across orientations

### Touch Targets
- Poster card: full-card tap, ≥116×164pt
- Hub chip: ≥70×40pt, 44pt min effective
- Tab bar icon: 22pt glyph, 44pt hit
- Play button: ≥44pt tall
- Back button: 24pt glyph in 40pt circle

### Safe Area Handling
- Top: solid top bar respects safe area + Dynamic Island; hubs row pinned directly below
- Bottom: tab bar respects home indicator; rows scroll above it
- Player: full-bleed edge-to-edge, controls inset from safe area
- Sides: 18pt content inset; rows intentionally bleed off the trailing edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0A0E2D`
- Canvas 2 (under hero): `#0E1438`
- Surface 1: `#161C44`
- Surface 2: `#1F2655`
- Divider: `#2A3266`
- Text primary: `#FFFFFF`
- Text secondary: `#AEB4D6`
- Text tertiary: `#6F77A6`
- Primary CTA (Play): `#0064FF` fill / `#FFFFFF` label
- Blue pressed: `#0052CC`
- Sky accent (active tab / eyebrow): `#4D9DFF`
- Glass secondary: `rgba(255,255,255,0.14)`
- Live / sports red: `#FF2D46`
- Rating gold: `#F5C518`

### Example Component Prompts
- "Build the Paramount+ brand-hubs row in SwiftUI: a horizontal scroller pinned directly under the top bar, 18pt leading inset, 8pt gaps, bleeds off the trailing edge. Each chip ≥70pt wide × 40pt tall, 8pt corner radius, filled with the network brand gradient (CBS `#0064FF→#0040A8`, MTV `#C8102E→#7A0A1C`, Nick `#FFB81C→#C98A00`, Showtime `#008C45→#045128`), centered network name in Inter 11pt w800 white (near-black on light networks). Tap re-skins browse; press scale 0.96."

- "Create the Paramount+ hero billboard: full-bleed key art ~46% of screen height directly below the hubs row, bottom scrim `linear-gradient(180deg, .clear 36%, rgba(10,14,45,0.72) 70%, #0A0E2D 100%)`. Left-aligned stack: eyebrow 'PARAMOUNT+ ORIGINAL' Inter 11pt w800 uppercase tracking 1.6pt color `#4D9DFF`; title Inter 26pt w800 `#FFFFFF`; meta Inter 12pt w400 `#AEB4D6` dot-separated; button pair — blue 'Play' (fill `#0064FF`, label white, 6pt radius, ▶ glyph) + glass '+ My List' (`rgba(255,255,255,0.14)` blurred)."

- "Render a Paramount+ Live TV & Sports row: 2:3 cards ~116pt wide, 8pt radius, full-bleed art. Top-left red 'LIVE' flag (`#FF2D46`, Inter 8pt w800 uppercase) with a 5pt pulsing white dot. Caption below Inter 11pt w500 `#AEB4D6`. Press: scale 0.97 over 120ms."

- "Build the Paramount+ bottom tab bar: 64pt + safe area, background `rgba(10,14,45,0.96)` blur 18px, 0.5pt top border `#2A3266`. Four tabs Home / Live TV / Search / My List, 22pt icons. Active = `#4D9DFF` filled glyph, inactive = `#6F77A6` stroked glyph, no tint pill. Labels Inter 12pt w600 always shown."

- "Create the Paramount+ Keep Watching progress bar: 4pt track height, 2pt radius, track `#1F2655`, fill `#0064FF` at the watched fraction. Label below Inter 11pt w500 `#AEB4D6` like 'S2 E5 · 22 min left'. Animate fill 0 → value over 400ms ease-out on appear."

- "Render the Paramount+ primary 'Play' button: fill `#0064FF`, label 'Play' Inter 15pt w700 `#FFFFFF` with a leading 14pt ▶ glyph, 6pt corner radius, 13pt/28pt padding. Pressed: `#0052CC` + scale 0.98. This blue is the single accent — never introduce a second."

### Iteration Guide
1. Canvas is midnight navy `#0A0E2D`, lifting to `#0E1438` under hero art — NEVER pure black, NEVER a light mode
2. Home leads with the brand-hubs row, *then* the hero — the network-bundle identity comes first
3. Paramount+ Blue `#0064FF` is the single accent for every CTA/progress/selection; sky-blue `#4D9DFF` only tints the active tab & eyebrows
4. Live TV & sports use red `#FF2D46` + a pulsing dot — a first-class pillar, not an afterthought
5. The hero is left-aligned with an 11pt uppercase tracked sky-blue eyebrow and a weight-900 title
6. Hub chips wear each network's brand color (CBS/MTV/Nick/Comedy Central/Showtime); tapping re-skins browse
7. Content rows are tight free-scroll poster strips that bleed off the trailing edge; no snap, no borders
8. Typography is heavy Inter (Paramount Sans) — hero/title 800-900, eyebrows 11pt uppercase tracked
9. Chrome is monochrome navy (white / `#AEB4D6` / `#6F77A6`) so full-color art is the loudest thing on screen
10. Motion is prestige-quiet — 120-400ms ease-out; hub re-skin cross-fades 300ms, live dot pulses on a 1.2s loop
