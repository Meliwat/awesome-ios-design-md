# Design System Inspiration of Peacock (iOS)

## 1. Visual Theme & Atmosphere

Peacock's iOS app is a cinematic, glow-in-the-dark streaming surface built around a single idea: the catalog is the interface. There is no light mode — the app is permanently dark, anchored on a deep indigo-black canvas (`#0A0A14`) that warms toward a violet-charcoal (`#140E26`) directly beneath hero artwork. The effect is that the home billboard appears to *emit* light into the surrounding chrome rather than sit on flat black, a deliberate departure from the pure-`#000` look of competitors. Open the Home tab and you are dropped into a full-bleed hero billboard — a single featured title occupying roughly the top half of the screen with a centered logo lockup, a colored eyebrow label ("New Original Series"), and a white "Play" button — followed by horizontally scrolling content rows: a rounded **Channels rail** of live network tiles (NBC, Bravo, USA, E!, SNL), then **Trending**, **Continue Watching**, **Channels you might like**, and genre rows, each a row of 2:3 poster cards with rank numerals and the multicolor "TOP 10" flag.

The signature brand asset is the **Peacock feather swoosh**: five quill strokes fanning out in yellow (`#FACC15`), orange (`#F97316`), pink (`#EC4899`), purple (`#8B5CF6`), and blue (`#2563EB`). This gradient is the *only* chromatic accent in the system — it appears as the logomark in the top bar, as the premium-upsell button fill, as selection underlines, and occasionally as a thin progress bar — but it is never used as a fill behind body text and never tints poster art. Everything else in the chrome is greyscale-on-indigo so the full-color thumbnails are the loudest thing on screen.

The other defining moment is the **white primary CTA**. Where most streamers tint their play button with a brand color, Peacock keeps "Play" solid white (`#FFFFFF`) with a near-black label (`#0A0A14`). This guarantees maximum contrast over any poster, signals "this is the one action that matters," and lets the feather gradient stay reserved for identity and upsell. Live and sports content (Peacock carries NBC Sports, Premier League, WWE, the Olympics) is marked with NBC's broadcast red (`#E5142B`) and a pulsing dot — the one place a saturated non-feather color is allowed.

Typography is Peacock Sans (a custom geometric grotesque); the closest free substitute is **Poppins**. The hierarchy is broadcast-promo loud: hero titles at 26-30pt weight 800, row headers at 22pt weight 700, all-caps tracked eyebrows at 11pt. Numerals are emphatic (rank "1", "2", "3" on Top 10 art). The type system is built to read at a glance from couch distance on the matching tvOS app and scale down cleanly to the phone.

**Key Characteristics:**
- Permanently dark — indigo-black canvas (`#0A0A14`) warming to violet (`#140E26`) under hero art; no light mode
- Full-bleed **hero billboard** — one featured title, centered logo lockup, colored eyebrow, white Play button
- Five-color **feather swoosh** (yellow→orange→pink→purple→blue) — the only chromatic accent; logomark, upsell, selection
- **White primary CTA** — "Play" is solid `#FFFFFF` with near-black label, never brand-tinted
- **Channels rail** — rounded live-network tiles (NBC, Bravo, USA, E!, SNL) in a horizontal strip
- **Content rows** — horizontally scrolling 2:3 poster cards; Top 10 rows show large rank numerals
- **Live / sports red** (`#E5142B`) with a pulsing dot — the one saturated non-feather color
- Broadcast-promo typography — Poppins (Peacock Sans fallback), hero 800, eyebrows all-caps tracked
- Bottom tab bar: Home / Channels / Search / My Stuff — dark, icon fill on active, no tint pill
- Premium upsell ("Get Premium") rendered with the feather gradient as a recurring monetization surface

## 2. Color Palette & Roles

Peacock ships dark-only. The "Light Mode" column below documents the rare light surfaces (account/legal web views, email-style screens) for completeness; the product UI uses the dark column everywhere.

### Primary (Interactive)
- **CTA White** (`#FFFFFF`): Primary action — "Play" / "Resume" button fill. The single most important color.
- **CTA White Pressed** (`#E6E6EA`): Pressed state of the white CTA + scale 0.98.
- **CTA Label** (`#0A0A14`): Near-black text/icon on the white CTA.
- **Glass Fill** (`rgba(255,255,255,0.14)`): Secondary action ("+ My Stuff", "More Info") — translucent over art.

### Feather Accent (Identity / Upsell / Selection)
- **Feather Yellow** (`#FACC15`): First quill; eyebrow labels, leading gradient stop.
- **Feather Orange** (`#F97316`): Second quill; Top-10 flag, progress fill start.
- **Feather Pink** (`#EC4899`): Center quill; gradient mid, premium button mid.
- **Feather Purple** (`#8B5CF6`): Fourth quill; gradient, radius/selection accent.
- **Feather Blue** (`#2563EB`): Fifth quill; gradient terminal stop.
- **Feather Gradient** (`linear-gradient(90deg, #FACC15, #F97316, #EC4899, #8B5CF6, #2563EB)`): Premium upsell, selection underline, logomark.

### Canvas & Surfaces (Light Mode — account / web views only)
- **Light Canvas** (`#FFFFFF`): Legal & web-account pages.
- **Light Surface** (`#F4F3F7`): Cards in account views.
- **Light Divider** (`#E2E0EA`): Hairlines in light web views.
- **Light Text** (`#0A0A14`): Body text in light web views.

### Canvas & Surfaces (Dark Mode — the product)
- **Canvas** (`#0A0A14`): Primary app background — deep indigo-black.
- **Canvas Warm** (`#140E26`): Background under hero billboard; violet glow.
- **Surface 1** (`#1A1530`): Cards, sheets, settings rows.
- **Surface 2** (`#241C3D`): Elevated chips, progress track, pressed rows.
- **Divider** (`#2C2545`): Hairline dividers, tab-bar top border.

### Text
- **Text Primary** (`#FFFFFF`): Titles, hero, primary labels on dark.
- **Text Secondary** (`#B5B2C2`): Metadata, captions, "See All".
- **Text Tertiary** (`#7A7689`): Inactive tabs, disabled, faint meta dots.

### Semantic
- **Live Red** (`#E5142B`): Live badge, sports "LIVE", channel-now indicator — pulsing dot.
- **Success Green** (`#1DB954`): Download-complete, save confirmation toast.
- **Error Red** (`#FF453A`): Playback error, payment failure.
- **Rating Gold** (`#FACC15`): Star rating / critic score (reuses Feather Yellow).

### Role → Color Table

| Role | Light Mode | Dark Mode (product) |
|------|-----------|---------------------|
| Canvas | `#FFFFFF` | `#0A0A14` |
| Canvas (hero) | `#FFFFFF` | `#140E26` |
| Surface 1 | `#F4F3F7` | `#1A1530` |
| Surface 2 | `#ECEAF3` | `#241C3D` |
| Divider | `#E2E0EA` | `#2C2545` |
| Text Primary | `#0A0A14` | `#FFFFFF` |
| Text Secondary | `#5B596B` | `#B5B2C2` |
| Text Tertiary | `#9A98A8` | `#7A7689` |
| Primary CTA | `#0A0A14` | `#FFFFFF` |
| CTA Label | `#FFFFFF` | `#0A0A14` |
| Live | `#E5142B` | `#E5142B` |
| Accent (feather) | feather gradient | feather gradient |

## 3. Typography Rules

### Font Family
- **Brand**: `Peacock Sans` — a proprietary geometric grotesque commissioned for the Peacock identity.
- **Closest Free Substitute**: `Poppins` (by Indian Type Foundry / Jonny Pinhorn, Google Fonts, SIL OFL) — geometric sans with circular bowls and a confident, broadcast-friendly tone.
- **Alternate Substitute**: `Montserrat` or `Sora` if Poppins is unavailable.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Lining, tabular for rank numerals and time codes; proportional elsewhere.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Poppins | 32pt | 800 | 1.1 | -0.5pt | "Home", "My Stuff" nav titles |
| Hero Title | Poppins | 28pt | 800 | 1.05 | -0.5pt | Featured billboard title (logo lockup may replace) |
| Row Header | Poppins | 22pt | 700 | 1.15 | -0.2pt | "Trending", "Continue Watching" |
| Card Title | Poppins | 18pt | 700 | 1.2 | -0.1pt | Detail-page / large-card title |
| Body | Poppins | 16pt | 600 | 1.5 | 0pt | Synopsis, descriptions |
| Body Regular | Poppins | 16pt | 400 | 1.5 | 0pt | Long-form legal / settings copy |
| Caption | Poppins | 15pt | 500 | 1.35 | 0pt | "S1 · E4 · 48 min" episode meta |
| Meta | Poppins | 13pt | 400 | 1.4 | 0pt | "2023 · Drama · TV-MA" dot-separated |
| Poster Caption | Poppins | 11pt | 500 | 1.25 | 0pt | Title under a poster card |
| Tab Label | Poppins | 12pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Eyebrow | Poppins | 11pt | 700 | 1.0 | 1.4pt (uppercase) | "NEW ORIGINAL SERIES" over hero |
| Button | Poppins | 15pt | 700 | 1.0 | 0pt | Primary/secondary button label |
| Rank Numeral | Poppins | 13pt | 800 | 1.0 | 0pt | "1" "2" "3" on Top-10 poster art |
| Badge | Poppins | 10pt | 800 | 1.0 | 0.4pt (uppercase) | "TOP 10", "NEW", "LIVE" |

### Principles
- **Broadcast-promo loudness**: Hero and row headers are heavy (700-800) — Peacock reads like an on-air promo, not a productivity app.
- **All-caps tracked eyebrows**: Category labels above the hero are 11pt uppercase with +1.4pt tracking — the signature framing device.
- **Numerals carry weight**: Rank numerals on Top-10 art and time codes use weight 800 tabular figures so "1 / 2 / 3" reads instantly.
- **Greyscale chrome, color content**: Type in the chrome is white / `#B5B2C2` only; chromatic feather color never sits behind text.
- **Dynamic Type**: Titles, body, captions scale; tab labels, eyebrows, badges, rank numerals stay fixed (layout-sensitive over art).

## 4. Component Stylings

### Buttons

**Primary Button (Play / Resume)**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#FFFFFF` (always white — never feather-tinted)
- Text/Icon: `#0A0A14`, Poppins 15pt weight 700, leading ▶ play glyph 14pt
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#E6E6EA` + scale 0.98
- Width: hugs content on hero; full-width on detail page

**Secondary / Glass Button (+ My Stuff / More Info / Trailer)**
- Background: `rgba(255,255,255,0.14)` with `backdrop-filter: blur(6px)`
- Text: `#FFFFFF`, Poppins 14pt weight 600
- Border: none
- Corner radius: 6pt
- Padding: 12pt vertical, 18-22pt horizontal
- Pressed: background `rgba(255,255,255,0.22)`

**Premium Upsell Button (Get Premium / Upgrade)**
- Background: feather gradient `linear-gradient(90deg,#F97316,#EC4899)` (orange→pink subset) or full 5-stop on large CTAs
- Text: `#FFFFFF`, Poppins 14pt weight 700
- Corner radius: 6pt
- Padding: 12pt vertical, 22pt horizontal
- The only place the gradient is a button fill

**Text Button (More Info / See All / Manage)**
- Background: transparent
- Text: `#B5B2C2`, Poppins 14pt weight 600
- No underline (selection underline uses feather gradient elsewhere)
- Pressed: text → `#FFFFFF`

**Icon Button (search, profile, add)**
- 21pt glyph, 44pt hit area
- Color `#FFFFFF`; inactive `#7A7689`
- No background

### Core Atoms

**Poster Card**
- 2:3 aspect ratio, ~112pt wide on phone (variable per row)
- Corner radius: 8pt
- Full-bleed key art, no border
- Optional rank numeral bottom-left in a 45%-black pill, Poppins 13pt w800
- Optional "TOP 10" / "NEW" badge top-left, feather-orange→pink gradient, Poppins 10pt w800
- Caption below: title, Poppins 11pt w500 `#B5B2C2`
- Pressed: scale 0.97, 150ms

**Channel Tile (Channels rail)**
- 60pt × 60pt rounded square, 14pt corner radius
- Filled with the network's brand gradient (NBC blue, Bravo purple, USA teal, E! orange, SNL red)
- Network mark/wordmark centered, white, Poppins 11pt w800
- Live tile gets a `#E5142B` "LIVE" pill top-right
- Pressed: scale 0.96

**Hero Billboard**
- Full-bleed key art occupying ~50% of screen height (360pt on phone)
- Bottom gradient scrim: `linear-gradient(180deg, transparent 38%, rgba(10,10,20,0.7) 70%, #0A0A14 100%)`
- Centered: eyebrow (11pt uppercase feather-yellow) → title or logo lockup → meta row (`13pt #B5B2C2` dot-separated) → button pair (white Play + glass +My Stuff)

**Live Badge**
- `#E5142B` fill, 4pt corner radius, Poppins 11pt w800 uppercase, 5pt/10pt padding
- Leading 7pt white dot that pulses (opacity 1 → 0.4, 1.2s loop)

**Progress Bar (Continue Watching)**
- 4pt track height, 2pt radius, track `#241C3D`
- Fill: feather orange→pink gradient
- Label below: Poppins 11pt w500 `#B5B2C2` — "S2 E6 · 24 min left"

**Top-10 Rank Numeral**
- Large outlined/filled numeral overlaying poster art OR pill numeral bottom-left
- Poppins weight 800, white, on 45%-black pill for legibility over varied art

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(10,10,20,0.96)` with `backdrop-filter: blur(18px) saturate(160%)`
- Top border: 0.5pt `#2C2545`
- Tabs (4): Home, Channels, Search, My Stuff
- Icon: 22pt; active = white **filled** glyph, inactive = `#7A7689` stroked glyph
- No tint pill — the fill swap is the only active treatment
- Labels: Poppins 12pt w600, always shown

**Top Bar (Home)**
- Height: 44pt + safe area
- Leading: feather logomark (30pt) + "peacock" wordmark (Poppins 19pt w800)
- Trailing: search glyph + profile/add (21pt, white)
- Transparent over hero; gains `#0A0A14` fill on scroll

**Detail Page**
- Full-bleed hero art + back chevron (top-left, white, on 40%-black circle)
- Sticky title bar appears on scroll past hero title

### Input Fields

**Search Bar**
- Height: 44pt
- Background: `#1A1530`
- Border: none (1pt feather-purple on focus)
- Corner radius: 8pt
- Leading magnifier 18pt `#7A7689`
- Placeholder: "Search shows, movies, channels" Poppins 14pt `#7A7689`
- Text: white

**Profile / PIN Entry**
- Large segmented digit boxes, `#1A1530` fill, 8pt radius, white digits, focus ring feather-purple

### Distinctive Components

**Feather Logomark Lockup**
- Five-quill SVG swoosh + lowercase "peacock" wordmark
- The brand signature; appears top-bar, splash, and as the centered hero lockup for Originals

**Channels Rail**
- Horizontal strip of network tiles directly under the hero — Peacock's identity as a *live TV + on-demand* hybrid
- Each tile is the network's brand color; live tiles carry a red "LIVE" pill

**Hero Billboard with Centered Logo Lockup**
- Featured Originals replace the text title with the show's logo treatment, centered, over key art — the most cinematic surface

**Top 10 Row**
- Dedicated row where each poster carries a large rank numeral; the numerals are part of the art composition

**Premium Upsell Surface**
- Feather-gradient banners/buttons recurring across Home, detail pages, and a "Plans" tab — the monetization layer rendered in brand color

**Live / Sports Lane**
- Rows of live event cards with `#E5142B` LIVE badges, live score strips, and "Watch Live" white CTAs — NBC Sports / Premier League / WWE surfaces

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48, 64
- Content horizontal inset: 18pt (rows bleed to the right edge for "more content" affordance)
- Inter-row vertical gap: 12-16pt
- Poster gap within a row: 10pt

### Grid & Container
- iPhone: 18pt side inset; rows scroll horizontally and bleed off the right edge
- iPad: rows show more posters per viewport; hero billboard taller (480pt)
- Hero billboard: ~50% of screen height on phone, full-bleed top
- No max content width — rows are edge-to-edge horizontal scrollers

### Whitespace Philosophy
- **Catalog density**: rows are packed tight (10pt gaps) so more art is visible — this is a content-discovery surface
- **Hero breathing room**: the billboard gets a generous scrim and centered composition so the featured title dominates
- **Chrome recedes**: minimal padding around chrome; the indigo canvas frames full-color art with no competing color

### Border Radius Scale
- Square (0pt): full-bleed hero art, scrims
- Small (4pt): badges, live pill, progress track
- Standard (6pt): buttons, search results
- Card (8pt): poster cards, search bar, settings rows
- Tile (14pt): channel tiles
- Comfortable (20pt): bottom sheets, modals
- Pill (500pt): filter chips, toggle
- Circle (50%): avatars, back-button background

## 6. Depth & Elevation

Peacock uses depth sparingly — the glow of the warm hero canvas is the primary "depth" cue, not drop shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Rows, posters, tabs on the indigo canvas |
| Hero Glow | Radial violet `#140E26` bleed behind art + bottom scrim | Featured billboard depth |
| Card Lift (Level 1) | `rgba(0,0,0,0.5) 0 8px 24px` | Focused/hovered poster (tvOS parity); pressed scale 0.97 |
| Floating (Level 2) | `rgba(0,0,0,0.55) 0 12px 32px` + 1pt `#2C2545` border | Bottom sheets, context menus, "Add to" sheet |
| Modal Overlay | `rgba(0,0,0,0.6)` | Dim behind sheets, age-gate, plan picker |

**Shadow Philosophy**: On a near-black indigo canvas, soft shadows barely register, so Peacock leans on (a) the warm violet glow under hero art and (b) a 1pt `#2C2545` border on floating panels as the elevation cue. Posters lift only via scale on press/focus. Nothing in the resting catalog casts a visible shadow — the full-color art against muted indigo provides all the figure/ground separation needed.

### Motion
- **Hero auto-rotate / parallax**: the featured billboard may cross-fade between titles every ~8s (400ms ease-in-out); art parallaxes subtly on scroll
- **Row scroll**: native momentum horizontal scroll; cards do not snap (free scroll), 60fps
- **Poster press**: scale 1.0 → 0.97 in 120ms ease-out; release springs back
- **Tab switch**: instant content swap; icon fill cross-fades 150ms
- **Detail open**: poster expands / pushes into full-bleed detail with 300ms ease-out shared-element-style transition
- **Live dot pulse**: opacity 1 → 0.4 → 1 over 1.2s, infinite ease-in-out
- **Sheet present**: bottom sheet slides up 300ms ease-out; dim fades in parallel
- **Progress fill**: animates from 0 to value over 400ms when a Continue Watching row appears
- **Haptic feedback**: light impact on poster select, soft on add-to-My-Stuff confirm

## 7. Do's and Don'ts

### Do
- Keep the app permanently dark on indigo-black `#0A0A14`; warm to `#140E26` under hero art
- Make "Play" solid white `#FFFFFF` with a near-black label — the single most important CTA
- Reserve the five-color feather gradient for logomark, premium upsell, and selection accents
- Use NBC red `#E5142B` + a pulsing dot for live & sports — the only saturated non-feather color
- Render the hero as a full-bleed billboard with a centered logo lockup for Originals
- Put the Channels rail (network tiles) high on Home — Peacock is a live-TV + on-demand hybrid
- Use heavy Poppins weights (700-800) for hero & row headers — broadcast-promo loudness
- Frame the hero with an 11pt uppercase tracked eyebrow (feather-yellow)
- Show large weight-800 rank numerals on Top-10 poster art
- Let full-color poster art be the loudest thing on screen; keep chrome greyscale

### Don't
- Don't add a light mode — Peacock is dark-only
- Don't tint the Play button with brand color — it is always white
- Don't use the feather gradient as a fill behind body text or as a poster overlay
- Don't use pure `#000000` for the canvas — the indigo `#0A0A14` / violet `#140E26` glow is the signature
- Don't use a tint pill on the active tab — the icon fill swap is the only active treatment
- Don't snap-scroll content rows — they are free momentum scrollers that bleed off the right edge
- Don't introduce a second accent color — feather + live-red is the entire chromatic budget
- Don't put borders on poster cards — art is full-bleed, separation comes from the indigo canvas
- Don't center body/synopsis text — only the hero lockup is centered; rows are left-aligned
- Don't animate aggressively — motion is cinematic and quiet (120-400ms ease-out)
- Don't let chrome compete with art — chrome stays white / `#B5B2C2` on indigo

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ~320pt tall; ~2.4 posters per viewport |
| iPhone 13/14/15 | 390pt | Standard layout; hero 360pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top bar |
| iPhone 15/16 Pro Max | 430pt | Hero 400pt; ~3 posters per viewport |
| iPad (portrait) | 768pt | Hero 480pt full-bleed; ~5 posters per row; 24pt insets |
| iPad (landscape) | 1024pt+ | Hero 560pt; ~7 posters per row; channels rail wider |

### Dynamic Type
- Hero title, row headers, body, captions: full scale
- Tab labels, eyebrows, badges, rank numerals, "LIVE": FIXED (composited over art / layout-sensitive)
- Synopsis on detail page scales to XL

### Orientation
- Phone: portrait-primary for browsing; player rotates to landscape full-screen
- iPad: both orientations for browse; player always landscape full-screen
- Hero art re-crops (focal point preserved) across orientations

### Touch Targets
- Poster card: full-card tap, ≥112×168pt
- Channel tile: 60×60pt, 44pt min effective
- Tab bar icon: 22pt glyph, 44pt hit
- Play button: ≥44pt tall
- Back button: 24pt glyph in 40pt circle

### Safe Area Handling
- Top: transparent top bar respects safe area + Dynamic Island; hero art bleeds under it
- Bottom: tab bar respects home indicator; rows scroll above it
- Player: full-bleed edge-to-edge, controls inset from safe area
- Sides: 18pt content inset; rows intentionally bleed off the trailing edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0A0A14`
- Canvas warm (under hero): `#140E26`
- Surface 1: `#1A1530`
- Surface 2: `#241C3D`
- Divider: `#2C2545`
- Text primary: `#FFFFFF`
- Text secondary: `#B5B2C2`
- Text tertiary: `#7A7689`
- Primary CTA (Play): `#FFFFFF` fill / `#0A0A14` label
- Glass secondary: `rgba(255,255,255,0.14)`
- Feather yellow: `#FACC15`
- Feather orange: `#F97316`
- Feather pink: `#EC4899`
- Feather purple: `#8B5CF6`
- Feather blue: `#2563EB`
- Feather gradient: `linear-gradient(90deg,#FACC15,#F97316,#EC4899,#8B5CF6,#2563EB)`
- Live / sports red: `#E5142B`

### Example Component Prompts
- "Build the Peacock hero billboard in SwiftUI: full-bleed key art occupying 50% of screen height, bottom scrim `linear-gradient(180deg, .clear 38%, rgba(10,10,20,0.7) 70%, #0A0A14 100%)`. Centered stack: eyebrow 'NEW ORIGINAL SERIES' in Poppins 11pt w700 uppercase tracking 1.4pt color `#FACC15`; title in Poppins 28pt w800 `#FFFFFF` (or a logo image lockup); meta row Poppins 13pt w400 `#B5B2C2` dot-separated; button pair — white 'Play' (fill `#FFFFFF`, label `#0A0A14`, 6pt radius, ▶ glyph) + glass '+ My Stuff' (`rgba(255,255,255,0.14)` blurred)."

- "Create the Peacock Channels rail: horizontal scroller, 18pt leading inset, 10pt gaps, bleeds off trailing edge. Each tile 60×60pt, 14pt corner radius, filled with the network brand gradient (NBC `#1E3A8A→#0F1E4D`, Bravo `#6D28D9→#3B1576`, USA `#0E7490→#073B47`), centered white wordmark Poppins 11pt w800. Live tiles get a `#E5142B` 'LIVE' pill top-right with a pulsing 7pt white dot."

- "Render a Peacock Top-10 poster row: 2:3 cards ~112pt wide, 8pt radius, full-bleed art. Bottom-left rank numeral in a 45%-black pill, Poppins 13pt w800 white. Top-left 'TOP 10' badge with `linear-gradient(90deg,#F97316,#EC4899)`, Poppins 10pt w800 uppercase. Caption below: Poppins 11pt w500 `#B5B2C2`. Press: scale 0.97 over 120ms."

- "Build the Peacock bottom tab bar: 64pt + safe area, background `rgba(10,10,20,0.96)` blur 18px, 0.5pt top border `#2C2545`. Four tabs Home / Channels / Search / My Stuff, 22pt icons. Active = white filled glyph, inactive = `#7A7689` stroked glyph, no tint pill. Labels Poppins 12pt w600 always shown."

- "Create the Peacock Continue Watching progress bar: 4pt track height, 2pt radius, track `#241C3D`, fill `linear-gradient(90deg,#F97316,#EC4899)` at the watched fraction. Label below in Poppins 11pt w500 `#B5B2C2` like 'S2 E6 · 24 min left'. Animate fill 0 → value over 400ms ease-out on appear."

- "Render the Peacock premium upsell button: feather gradient fill `linear-gradient(90deg,#F97316,#EC4899)` (or full 5-stop on large CTAs), label 'Get Premium' Poppins 14pt w700 `#FFFFFF`, 6pt corner radius, 12pt/22pt padding. This is the only place the gradient is used as a button fill."

### Iteration Guide
1. Canvas is indigo-black `#0A0A14`, warming to violet `#140E26` under hero art — NEVER pure black, NEVER a light mode
2. "Play" is solid white `#FFFFFF` with `#0A0A14` label — never brand-tinted; it is the single hero action
3. The feather gradient (yellow→orange→pink→purple→blue) is identity/upsell/selection only — never behind text, never over art
4. Live & sports use NBC red `#E5142B` + a pulsing dot — the only saturated non-feather color
5. Home opens on a full-bleed billboard hero (centered logo lockup for Originals) over the warm canvas
6. The Channels rail of network-brand tiles sits high on Home — Peacock is live-TV + on-demand
7. Content rows are tight free-scroll poster strips that bleed off the trailing edge; no snap, no borders
8. Typography is loud Poppins (Peacock Sans) — hero/header 700-800, eyebrows 11pt uppercase tracked
9. Chrome is greyscale (white / `#B5B2C2` / `#7A7689`) so full-color art is the loudest thing on screen
10. Motion is cinematic and quiet — 120-400ms ease-out; live dot pulses on a 1.2s loop
