# Design System Inspiration of Max (iOS)

## 1. Visual Theme & Atmosphere

Max's iOS app is a prestige-cinema surface built on a deep, saturated purple canvas (`#12053A`) that feels less like a neutral void and more like a darkened theater drenched in brand color. Where most streamers default to near-black, Max commits to its identity: the entire background is a deep blue-violet, and elevated surfaces (`#1B0B4D`, `#250F5E`) step up within the same purple family rather than toward gray. The effect is immersive and premium — the chrome itself carries the brand, so even an empty screen reads as "Max."

The accent system is a gradient, not a single hue. The Max brand gradient sweeps from electric blue (`#0046FF`) to violet (`#7B2FF7`) and powers the wordmark, the primary "Play" CTA, focus rings, and the auto-playing hero billboard's framing. A solid fallback (`#5A2BE0`) is used where a gradient can't render (small toggles, tab indicator). This is the design equivalent of a marquee light — the gradient is reserved for brand and primary action moments, while everything else is white, lavender-gray, or deep purple. The restraint around the gradient is what keeps it feeling prestige rather than gaudy.

Typography is Inter (standing in for Max's clean grotesque brand face), with a compact, confident hierarchy from 11-32pt at weights 400 / 600 / 800. There are no decorative display fonts — the content artwork and the auto-trailer billboard are the spectacle. Type's role is to label and badge: row titles at 20-22pt weight 800, tile titles at 15pt weight 600, metadata in lavender-gray at 13pt weight 400, and the signature "Max Originals" badge in 11pt weight 800 uppercase. The most expressive type moment is the billboard, where a show's logo art (or 32pt weight 800 title) sits over a slowly cross-fading auto-trailer.

**Key Characteristics:**
- Deep purple canvas (`#12053A`) — brand color as the entire environment, not a neutral dark
- Max brand gradient (`#0046FF → #7B2FF7`) — wordmark, primary CTA, focus, billboard framing
- Auto-trailer hero billboard — a full-bleed muted trailer that cross-fades into the next title
- Prestige content rows — large 16:9 / 2:3 tiles with restrained, premium spacing
- "Max Originals" badge — gradient-edged pill marking first-party prestige content
- Profile gate — full-screen avatar grid on launch (HBO-lineage "who's watching")
- Solid violet fallback (`#5A2BE0`) for toggles and the tab indicator where gradients can't render
- Inter typeface (clean grotesque), weights 400/600/800
- Trailer crossfade + a subtle gradient shimmer on the CTA

## 2. Color Palette & Roles

### Primary (Brand Gradient)
- **Gradient Start** (`#0046FF`): Electric blue — left/top stop of the Max brand gradient.
- **Gradient End** (`#7B2FF7`): Violet — right/bottom stop of the Max brand gradient.
- **Gradient Solid** (`#5A2BE0`): Solid violet fallback — toggles, tab indicator, small UI where a gradient can't render.
- **Gradient Pressed** (`#4A22BD`): Pressed/active state for the solid fallback and CTA overlay.
- **Gradient Glow** (`rgba(91,43,224,0.35)`): Soft glow under the primary CTA and around focused tiles.

### Canvas & Surfaces
- **Canvas** (`#12053A`): Primary deep-purple app background — the branded environment.
- **Deep Black** (`#000000`): Full-screen video player background only.
- **Surface 1** (`#1B0B4D`): Card backgrounds, sheets, elevated rails, details metadata panel.
- **Surface 2** (`#250F5E`): Higher elevation — modals, active rows, chip default background.
- **Surface 3** (`#36206E`): Pressed state on purple surfaces, hairline divider tone.
- **Divider** (`#36206E`): 1pt separators between settings rows and metadata blocks.

### Text
- **Text Primary** (`#FFFFFF`): Show titles, screen headers, primary labels.
- **Text Secondary** (`#B0A8D0`): Lavender-gray — metadata, descriptions, row subtitles.
- **Text Tertiary** (`#7E76A6`): Disabled labels, very low-emphasis captions, placeholder text.

### Semantic
- **Live Gold** (`#F2C94C`): "LIVE" sports badge (Bleacher Report / live events) and now-playing marker.
- **Badge Surface** (`#250F5E`): "TV-MA" / "R" maturity-rating chip background, text `#FFFFFF`.
- **Originals Gradient** (`#0046FF → #7B2FF7`): "Max Originals" badge border/fill.
- **Error Red** (`#FF5C7A`): Playback errors, download failures, offline state.

### Light Mode (Limited Use)
Max's iOS app is effectively dark-only — the prestige-theater atmosphere depends on the deep-purple canvas. A light variant is not part of the core product.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#12053A`)

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (standing in for Max's clean grotesque brand typeface)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for runtime, episode counts, and live times

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Billboard Title | Inter | 32pt | 800 | 1.1 | -0.5pt | Show title on the auto-trailer billboard (when no logo art) |
| Screen Title (Large) | Inter | 28pt | 800 | 1.15 | -0.4pt | "My List", "Series" large nav title |
| Row Header | Inter | 22pt | 800 | 1.2 | -0.3pt | "Max Originals", "Trending Now" |
| Section Header | Inter | 20pt | 800 | 1.2 | -0.3pt | Sub-rows inside a category |
| Episode Title | Inter | 16pt | 600 | 1.25 | -0.1pt | Episode row title in details |
| Tile Title | Inter | 15pt | 600 | 1.3 | -0.1pt | Show title under a tile |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Synopsis, episode description |
| Metadata | Inter | 13pt | 400 | 1.3 | 0pt | "2024 · TV-MA · 1 Season" |
| Tile Subtitle | Inter | 12pt | 400 | 1.3 | 0pt | "S2 · E1" / "New episode" |
| Label (UPPER) | Inter | 11pt | 800 | 1.2 | 0.6pt | "TRENDING NOW" eyebrow labels |
| Button (Primary) | Inter | 16pt | 800 | 1.0 | 0.2pt | Gradient "Play" button |
| Button (Secondary) | Inter | 15pt | 600 | 1.0 | 0pt | "More Info", outline buttons |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Badge | Inter | 11pt | 800 | 1.0 | 0.4pt | "Max Originals", "LIVE", rating pill |

### Principles
- **Weights concentrated at 400 / 600 / 800**: Regular for body/metadata, semibold for titles, extrabold for headers, CTAs, and badges — no thin, no medium
- **Content is the spectacle**: No decorative display type; the auto-trailer billboard and artwork carry drama
- **Negative tracking on big type, neutral on small**: -0.5pt at 32pt easing to 0pt at body
- **All-caps only for eyebrows and badges**: "TRENDING NOW", "MAX ORIGINALS" — never for titles
- **Gradient is brand-only**: gradient fills appear on the wordmark, primary CTA, focus, and Originals badge — never on body text

## 4. Component Stylings

### Buttons

**Primary Play CTA (The Gradient Button)**
- Shape: Rounded rectangle, height 52pt, full-width within content margins (auto-width on billboard)
- Background: linear gradient 135° from `#0046FF` to `#7B2FF7`
- Text: `#FFFFFF`
- Icon: SF Symbol `play.fill` 18pt leading the label, also `#FFFFFF`
- Corner radius: 8pt
- Font: Inter 16pt weight 800, letter-spacing 0.2pt
- Shadow: `rgba(91,43,224,0.35) 0 8px 24px` — a soft brand glow
- Pressed: a `rgba(74,34,189,0.55)` overlay darkens the gradient, scale 0.97, `.impactOccurred(.light)` haptic
- Idle: a slow 4s diagonal shimmer sweeps across the gradient

**Secondary Button ("More Info")**
- Background: `rgba(255,255,255,0.14)`
- Text: `#FFFFFF`
- Height: 52pt, corner radius 8pt
- Font: Inter 15pt weight 600
- Pressed: background `rgba(255,255,255,0.22)`, scale 0.98

**Icon Actions (My List +, Download, Share)**
- Size: 24pt glyph, 44pt hit area
- Default: `#FFFFFF`
- Active: gradient-tinted (use `#5A2BE0` solid where a gradient mask is impractical) — added to My List, download complete
- Layout: vertical icon-over-label stacks on the details action row, 32pt gap

**Profile Avatar (gate)**
- 96pt circle on the launch gate grid, 2pt gradient ring when focused
- Name label below, Inter 15pt weight 600 `#FFFFFF`
- Pressed: scale 0.95 + ring brightens

**Text Button ("See All")**
- Font: Inter 13pt weight 600
- Color: `#B0A8D0`
- No underline, 44pt hit area, trailing each row header

### Cards & Containers

**Content Tile (Horizontal row)**
- Width: 200pt (16:9 standard) or 130pt (2:3 poster, used in movie rows)
- Aspect: 16:9 or 2:3
- Corner radius: 6pt on the artwork
- Gap between tiles: 12pt
- Structure: artwork → optional title (15pt w600, 1-line) → subtitle (12pt w400 `#B0A8D0`)
- "Max Originals" tiles carry a small gradient-edge badge top-left
- Press: scale 1.04 with focus lift `rgba(0,0,0,0.55) 0 12px 28px` and a 2pt gradient ring

**Billboard (auto-trailer hero)**
- Full-bleed, height ~ 460pt (top of Home)
- A muted auto-playing trailer fills the frame; a bottom-up gradient `rgba(18,5,58,0) → #12053A` anchors text
- Show logo art (or 32pt w800 title) bottom-left
- Metadata line "2024 · TV-MA · 1 Season · Drama" 13pt w400 `#B0A8D0`
- Action row: gradient "Play" CTA + `+ My List` + `More Info`
- Trailer cross-fades to the next billboard title every ~20s with a 1.2s dissolve

**Max Originals Row**
- Header "Max Originals" 22pt w800 (with a small gradient accent mark) + "See All" trailing
- Larger tiles (220pt), each with the gradient-edge Originals badge

**Details Hero**
- Full-bleed 16:9 backdrop still, height ~ 280pt, darkened bottom-up to `#12053A`
- Logo art (or 32pt w800 title) anchored bottom-left
- Metadata + gradient "Play" CTA + `+ My List` + `Download` + `Share` vertical icon stacks
- Below: synopsis (15pt w400, 3-line clamp + "More"), then season picker + episode list

**Episode Row**
- Height: 96pt
- Layout: 140pt × 79pt (16:9) thumbnail → stacked title (16pt w600) + "S2 · E1 · 58 min" meta (13pt w400 `#B0A8D0`) + 2-line description → trailing download icon
- Background: transparent (`#12053A`)
- Pressed: background `#1B0B4D`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(18,5,58,0.94)` with `.regularMaterial` blur
- Tabs: Home, Search, My List
- Icon size: 24pt
- Active color: `#FFFFFF` with a 3pt solid `#5A2BE0` underline indicator (gradient can't render reliably at this size — solid fallback)
- Inactive: `#B0A8D0`
- Labels: Inter 10pt w600, always shown
- Active tab icon: filled SF Symbol; inactive: outlined

**Top Nav (Home)**
- Height: 48pt + safe area
- Max gradient wordmark top-left, 22pt
- Profile avatar (28pt circular) top-right
- Transparent over the billboard, fades to `rgba(18,5,58,0.94)` blur on scroll

**Player Controls Overlay**
- Tap video → controls fade in over a `rgba(0,0,0,0.55)` scrim
- Center: 64pt circular `rgba(255,255,255,0.16)` button with white `play.fill` / `pause.fill`, ±10s skip flanking at 28pt
- Bottom: gradient scrubber (2pt track `rgba(255,255,255,0.3)`, gradient fill `#0046FF→#7B2FF7`, 14pt white thumb), elapsed/remaining 12pt
- Top: title + close (✕); bottom-right: captions, audio, AirPlay

### Input Fields

**Search Bar**
- Background: `#250F5E`
- Height: 44pt, corner radius 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#B0A8D0`
- Placeholder: "Search Max", 16pt w400 `#B0A8D0`
- Text color: `#FFFFFF`
- Focus: 1.5pt gradient ring (solid `#5A2BE0` fallback)

**Filter / Genre Chip**
- Background: `#250F5E` default / gradient `#0046FF→#7B2FF7` selected
- Text: `#FFFFFF` (both states)
- Height: 36pt, corner radius 500pt (full pill), horizontal padding 16pt
- Font: Inter 14pt weight 600

### Distinctive Components

**Gradient-Brand Hero Billboard**
- The signature Max move: a full-bleed muted auto-trailer at the top of Home, framed by the deep-purple gradient fade, with the gradient "Play" CTA pinned over it. The trailer cross-fades to the next title every ~20s. This is the prestige-marquee moment — brand gradient, motion, and content fused.

**Max Originals Badge**
- A small pill with a gradient border (or gradient fill) and "Max Originals" in 11pt w800 uppercase, marking first-party prestige content on tiles, details, and the billboard.

**Profile Gate**
- On launch, a full-screen grid of large 96pt circular avatars ("Who's watching?") over the purple canvas — the HBO-lineage profile selector. Focused avatar gets a 2pt gradient ring.

**Trailer Crossfade + Gradient Shimmer**
- The billboard trailer dissolves between titles (1.2s); the primary CTA carries a slow 4s diagonal gradient shimmer to signal it as the brand action.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard margin: 16pt horizontal
- Between rows: 28pt vertical
- Tile gap: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal rows: begin at 16pt left inset, peek last tile at right edge
- My List grid: 3-column (16:9) or 3-column (2:3 movies), 12pt gap
- Profile gate: 2-column avatar grid, centered, 32pt gap

### Whitespace Philosophy
- **Prestige spacing**: Rows sit 28pt apart with restrained metadata — more breathing room than a budget streamer, signaling premium
- **Billboard dominance**: The auto-trailer billboard owns ~460pt before any rows — the spectacle leads
- **Tight tile gaps within rows**: 12pt keeps the row scannable while each tile stays large

### Border Radius Scale
- Sharp (0pt): Full-bleed billboard, details backdrop, video surface
- Soft (6pt): Content tiles, posters, hero thumbnails
- Standard (8pt): Buttons, search bar, cards
- Comfortable (12-16pt): Bottom sheets, modals
- Full Pill (500pt): Genre/filter chips, "Max Originals" badge, "LIVE" badge
- Circle (50%): Profile avatars, player center button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, rows, list rows |
| Tile Focus (Level 1) | `rgba(0,0,0,0.55) 0 12px 28px` | Tile lifted on press/focus |
| Card (Level 2) | `rgba(0,0,0,0.45) 0 6px 18px` | Sheets, metadata panels |
| Brand Glow (Accent) | `rgba(91,43,224,0.35) 0 8px 24px` | Under the gradient CTA, focused-tile gradient ring |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 44px` | Bottom sheets, episode-options menu |
| Tab Bar Material | `.regularMaterial` over `rgba(18,5,58,0.94)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On the deep-purple canvas, neutral shadows must be deep (0.45-0.55 opacity, 18-28pt blur) to register. The signature elevation cue is chromatic and brand-aligned: the violet glow under the gradient CTA and the gradient ring on a focused tile communicate lift where neutral shadows can't. Everything else is flat — depth is surface-step color within the purple family, never borders.

### Motion
- **Tile press**: scale 1.0 → 1.04 over 180ms ease-out, focus shadow + gradient ring fade in, `.impactOccurred(.light)` haptic
- **Play CTA tap**: scale 0.97 spring (damping 0.7), gradient darkens, `.impactOccurred(.light)`
- **Billboard trailer crossfade**: 1.2s opacity dissolve between consecutive titles every ~20s
- **CTA gradient shimmer**: a slow 4s diagonal highlight sweep, infinite
- **Row scroll**: native momentum, last tile peeks; no snap
- **Details enter**: backdrop cross-dissolves in over 250ms, rows slide up 12pt + fade
- **Player controls**: fade in/out over 200ms with the scrim

## 7. Do's and Don'ts

### Do
- Use `#12053A` deep purple as the canvas — the brand color IS the environment, not a neutral dark
- Reserve the gradient (`#0046FF→#7B2FF7`) for the wordmark, primary CTA, focus, and Originals badge
- Use the solid violet fallback (`#5A2BE0`) for the tab indicator and small toggles where a gradient can't render
- Lead Home with the auto-trailer billboard and cross-fade between titles
- Mark first-party content with the gradient-edge "Max Originals" badge
- Step elevation within the purple family (`#1B0B4D`, `#250F5E`) — not toward gray
- Put the violet glow under the gradient CTA — it's the chromatic elevation cue
- Use white text on the gradient button — white on the blue-violet gradient is correct

### Don't
- Don't use near-black or gray as the canvas — Max's identity is the deep-purple environment
- Don't apply the gradient to body text or decorative elements — it's brand/action-only
- Don't try to render the full gradient on the tiny tab indicator — use the `#5A2BE0` solid fallback
- Don't use thin or medium weights — Inter here is 400 / 600 / 800 only
- Don't round content tiles heavily — 6pt max keeps artwork integrity
- Don't crowd the rows — prestige spacing (28pt) is part of the premium signal
- Don't introduce visible card borders — elevation is surface-step color, not strokes
- Don't over-animate — tile scale is subtle (1.04); the only continuous motion is the slow billboard crossfade and CTA shimmer

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 170pt 16:9 tiles, billboard 400pt |
| iPhone 13/14/15 | 390pt | Standard 200pt tiles, billboard 460pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | 230pt tiles, billboard scales to 520pt |
| iPad | 768pt+ | 4-column My List grid, larger billboard, rows show 4-5 tiles |

### Dynamic Type
- Show titles, episode titles, synopsis: full scale
- Tile subtitle / metadata: scales but clamps at +2 steps (layout-sensitive in rows)
- Play CTA label: scales to 18pt max; button height fixed at 52pt
- Tab labels, badges, scrubber times: fixed (never scale)

### Orientation
- Home / Search / My List / Details / Profile gate: **portrait-locked**
- Video player: **rotates to landscape** — auto-enters full-screen landscape on play
- Billboard trailer: portrait, muted; full-screen on tap

### Touch Targets
- Play CTA: 52pt height, full-width — impossible to miss
- Content tiles: full-tile tappable, ≥ 110pt tall hit
- Episode rows: 96pt height, full-row tappable
- Profile avatars: 96pt circle, generous gate spacing
- Tab bar icons: 24pt icon, 44pt effective hit

### Safe Area Handling
- Top: transparent nav respects safe area / Dynamic Island over the billboard
- Bottom: tab bar floats above home indicator; player controls inset from it
- Sides: 16pt content margins; rows bleed to the right edge intentionally

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#12053A`
- Surface 1 (cards): `#1B0B4D`
- Surface 2 (sheets/chips): `#250F5E`
- Surface 3 / Divider: `#36206E`
- Primary text: `#FFFFFF`
- Secondary text: `#B0A8D0`
- Tertiary text: `#7E76A6`
- Gradient start: `#0046FF`
- Gradient end: `#7B2FF7`
- Gradient solid (fallback): `#5A2BE0`
- Live gold: `#F2C94C`
- Error red: `#FF5C7A`

### Example Component Prompts
- "Create a SwiftUI Max primary Play button: full-width, 52pt tall, 8pt corner radius, background a 135° linear gradient from #0046FF to #7B2FF7, leading SF Symbol 'play.fill' 18pt + label 'Play' in Inter 16pt weight 800, both #FFFFFF. Soft brand glow shadow rgba(91,43,224,0.35) 0 8px 24px. Pressed: a rgba(74,34,189,0.55) overlay, scale 0.97, light haptic. Idle: a slow 4s diagonal shimmer across the gradient."
- "Build the Max hero billboard: full-bleed 460pt-tall auto-trailer frame with a bottom-up gradient from rgba(18,5,58,0) to #12053A. Show title 'Dune: Prophecy' bottom-left in Inter 32pt weight 800 #FFFFFF, metadata '2024 · TV-MA · 1 Season · Sci-Fi' in 13pt weight 400 #B0A8D0, a small gradient-bordered 'Max Originals' pill above it. Action row: gradient Play CTA, '+ My List', 'More Info'. Cross-fade to the next title every ~20s with a 1.2s dissolve."
- "Design a Max Originals tile: 220×124pt (16:9) artwork, 6pt corner radius, with a small gradient-edge 'Max Originals' badge top-left (Inter 11pt weight 800 uppercase #FFFFFF). On press: scale 1.04 with shadow rgba(0,0,0,0.55) 0 12px 28px and a 2pt gradient ring (#0046FF→#7B2FF7)."
- "Create the Max profile gate: deep-purple #12053A full screen, centered title 'Who's watching?' in Inter 28pt weight 800 #FFFFFF, a 2-column grid of 96pt circular avatars with names below in 15pt weight 600. Focused avatar gets a 2pt gradient ring; pressed scales to 0.95."
- "Build the Max bottom tab bar: 52pt + safe area, background rgba(18,5,58,0.94) with regularMaterial blur. Tabs Home, Search, My List. Active icon + label #FFFFFF with a 3pt solid #5A2BE0 underline indicator (gradient fallback at small size), inactive #B0A8D0. Labels Inter 10pt weight 600, filled SF Symbol when active."

### Iteration Guide
1. Canvas is `#12053A` deep purple — the brand color is the environment, NOT a neutral dark
2. The gradient (`#0046FF→#7B2FF7`) is brand/action-only — wordmark, Play CTA, focus, Originals badge
3. Use the solid violet `#5A2BE0` fallback wherever a gradient can't render cleanly (tab indicator, tiny toggles)
4. Lead Home with the auto-trailer billboard; cross-fade between titles every ~20s
5. White text on the gradient button — white on the blue-violet gradient is correct
6. Step elevation within the purple family (`#1B0B4D`, `#250F5E`), never toward gray
7. Elevation on dark = surface-step color + the violet glow / gradient ring, never borders
8. Typography is Inter, weights 400/600/800 only — no thin, no medium; prestige 28pt row spacing
