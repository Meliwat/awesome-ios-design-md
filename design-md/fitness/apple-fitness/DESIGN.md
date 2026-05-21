# Design System Inspiration of Apple Fitness (iOS)

## 1. Visual Theme & Atmosphere

Apple Fitness is the purest expression of "the data *is* the brand" in Apple's app catalog. The entire identity rests on **three concentric Activity rings** — **Move** (`#FA114F`, a hot pink-red), **Exercise** (`#92E82A`, electric lime-green), and **Stand** (`#1EE4E1`, bright cyan) — rendered as thick, rounded, glowing arcs on a **true-black canvas** (`#000000`). There is no logo wordmark doing the work here; the rings *are* the logo, and the whole app is engineered to make those three colors the brightest, most saturated thing on a pure-black screen. Everything else — typography, surfaces, chrome — is restrained Apple system styling so the rings and the Fitness+ artwork carry all the energy.

The signature surface is the **Summary tab**. It opens with a day eyebrow + large date, a "Summary" large title, then the hero **Activity rings card**: the three rings drawn large on a grouped `#1C1C1E` card with a legend listing Move/Exercise/Stand values against goals in tabular numerals (each label in its ring's color). Below that flows a grid of metric tiles (Steps, Distance, Heart Rate, Workouts) — small grouped cards with a tinted SF Symbol, a label, and a big value — and then the **Fitness+ shelf**: a horizontally scrolling row of cinematic 200pt workout thumbnails (HIIT, Yoga, Strength, Cycling), each with a frosted-glass badge and play button, an uppercase type label, a title, and the trainer + playlist meta. The second pillar is **Fitness+ itself** — a full streaming catalog browsed as artwork-forward shelves, and the in-workout screen where your rings overlay the trainer video and update live.

Typography is **SF Pro** (San Francisco) — Apple's system face — set the way Apple sets it everywhere: large titles bold and tight, section headers heavy, body at 17pt regular-to-semibold, and Apple's signature **label-opacity ramp on dark** (primary white 100%, secondary white 60%, tertiary white 30%) rather than distinct gray hexes. Numbers are tabular so the ring legend and metric tiles never jitter. The voice is encouraging but never loud — Apple lets the rings emote; the type stays calm and legible.

Chrome is minimal and is pure Apple HIG. Cards are grouped, rounded (14–18pt), and sit on true black with subtle elevation from surface lightness rather than drop shadows. The bottom tab bar (Summary, Fitness+, Sharing) uses the system blurred material and tints the active tab Move-pink (`#FF375F`) — the one place an accent appears in chrome. There is genuine restraint: the design system's job is to be an invisible, premium frame around three glowing rings and a wall of beautiful workout artwork.

**Key Characteristics:**
- True-black canvas (`#000000`) — the three rings are the brightest thing on screen
- The brand *is* the three rings: Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`
- Each ring sits on a 22%-opacity track of its own color; arcs are thick, round-capped, glowing
- Grouped rounded system cards (`#1C1C1E`, 14–18pt radius) — depth from lightness, not shadow
- SF Pro typography with Apple's label-opacity ramp (100% / 60% / 30% on dark)
- Tabular numerals so the ring legend and metric tiles never jitter
- Summary tab = ring hero card + metric tile grid + Fitness+ artwork shelf
- Fitness+ = cinematic full-color workout thumbnails with frosted-glass overlays
- Move-pink (`#FF375F`) as the single chrome accent (active tab, links, primary CTA)
- Fitness+ purple (`#C969E0`) for Fitness+ surfaces; ring colors never restyle
- In-workout: your three rings overlay the trainer video and animate live
- Restrained, premium, Apple-HIG chrome — the system is an invisible frame

## 2. Color Palette & Roles

### Primary — The Three Activity Rings (the brand)
These are the identity. They are fixed, semantic, and **never restyle across themes or contexts**.
- **Move** (`#FA114F`): Move ring (active energy / kcal). Label/accent on dark brightens to **`#FF375F`**.
- **Exercise** (`#92E82A`): Exercise ring (brisk-activity minutes). Bright variant **`#66FF00`**.
- **Stand** (`#1EE4E1`): Stand ring (hours stood). Bright variant **`#00F0FF`**.
- **Ring Track**: each ring's own color at **22% opacity** (`rgba(250,17,79,0.22)` / `rgba(146,232,42,0.22)` / `rgba(30,228,225,0.22)`) — the unfilled groove.

### Interactive / Chrome Accent
- **Move-Pink Accent** (`#FF375F`): The single chrome accent — active tab tint, links, "See All", primary CTA. (Move ring color, label-brightened.)
- **Accent Pressed** (`#D80E45`): Pressed state for the primary button.
- **Fitness+ Purple** (`#C969E0`): Fitness+ brand surfaces, badges, and program artwork tinting.

### Canvas & Surfaces (Dark — primary on iPhone)
- **True Black Canvas** (`#000000`): App background. Pure black, always (iPhone).
- **Grouped Surface 1** (`#1C1C1E`): System grouped cards (ring card, tiles) — `systemGray6` dark.
- **Surface 2** (`#2C2C2E`): Nested cells, secondary grouped background.
- **Surface 3** (`#3A3A3C`): Pressed surfaces, control fills.
- **Separator** (`#38383A`): Hairline separators inside grouped lists.
- **Fill (Quaternary)** (`rgba(118,118,128,0.24)`): Neutral button fill, segmented track.

### Canvas & Surfaces (Light — iPad / system light)
- **Light Canvas** (`#F2F2F7`): `systemGroupedBackground` light.
- **Light Surface** (`#FFFFFF`): Grouped cards on light.
- **Light Separator** (`#C6C6C8`): Hairline separators.

### Text (Apple label-opacity ramp, not fixed grays)
- **Label Primary** (`#FFFFFF`, 100%): Titles, values on dark.
- **Label Secondary** (`#EBEBF5` @ 60% → `#EBEBF599`): Subtitles, meta.
- **Label Tertiary** (`#EBEBF5` @ 30% → `#EBEBF54D`): Captions, disabled, low-emphasis.
- **Label (light)** (`#000000` / `#3C3C4399` / `#3C3C434D`): Same ramp inverted for light mode.

### Semantic
- **Success / Closed** (`#30D158`): Goal-met confirmation (distinct from Exercise green).
- **Error** (`#FF453A`): Errors, destructive actions.
- **Warning** (`#FF9F0A`): Low-data, caution.
- **Award Gold** (`#FFD60A`): Achievement / award badges, streak.

## 3. Typography Rules

### Font Family
- **All text**: `SF Pro` (San Francisco — `SF Pro Display` ≥ 20pt, `SF Pro Text` < 20pt), Apple's system typeface, accessed via the system font (no bundling needed on iOS).
- **Cross-platform fallback**: `Inter` (SIL OFL) — the closest geometric-humanist substitute when SF Pro is unavailable (web/Android).
- **Numerals**: SF Pro with **tabular figures** (`.monospacedDigit()` / `font-variant-numeric: tabular-nums`) — ring legend, metric tiles, and live workout values must not jitter.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 40pt | 800 | 1.1 | -1.0pt | "Summary" screen large title |
| Date | SF Pro Display | 32pt | 800 | 1.1 | -0.6pt | The big date under the day eyebrow |
| Header | SF Pro Display | 26pt | 800 | 1.15 | -0.5pt | Ring detail header ("Move") |
| Section | SF Pro Display | 22pt | 800 | 1.2 | -0.4pt | "Fitness+", "Awards" |
| Title 3 | SF Pro Display | 20pt | 700 | 1.25 | -0.3pt | Sub-section title |
| Body | SF Pro Text | 17pt | 600 | 1.4 | -0.2pt | Primary body / emphasized list |
| Body (regular) | SF Pro Text | 17pt | 400 | 1.5 | -0.2pt | Descriptive copy |
| Ring Value | SF Pro Display | 19pt | 800 | 1.1 | 0pt | Legend value (tabular) |
| Tile Value | SF Pro Display | 22pt | 800 | 1.1 | 0pt | Metric tile value (tabular) |
| Callout / Card Title | SF Pro Text | 15pt | 600 | 1.3 | -0.2pt | Fitness+ card title |
| Footnote | SF Pro Text | 13pt | 400 | 1.35 | 0pt | Trainer · playlist, sub-meta |
| Ring Label / Eyebrow | SF Pro Text | 12pt | 700 | 1.2 | 0.4pt (UPPERCASE) | "MOVE" / "THURSDAY" — in ring color |
| Button | SF Pro Text | 17pt | 600 | 1.0 | -0.2pt | Primary / tinted buttons |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0pt | Bottom tab labels |
| Badge | SF Pro Text | 10pt | 700 | 1.0 | 0.5pt (UPPERCASE) | "NEW" / "TRENDING" frosted badge |

### Principles
- **Apple HIG type, not custom**: Sizes, weights, and the Dynamic Type ramp follow Apple's text styles (Large Title, Title, Body, Footnote, Caption) — don't invent a bespoke scale.
- **Label-opacity, not gray hexes**: Secondary/tertiary text is white at 60% / 30% opacity (Apple's `secondaryLabel` / `tertiaryLabel`), so it adapts to any background.
- **Rings emote, type stays calm**: Headlines are bold but the system never shouts — the three glowing rings carry the energy.
- **Ring labels wear ring colors**: The one place text is brand-colored is the Move/Exercise/Stand eyebrow labels (in their ring color, label-brightened on dark).
- **Tabular everywhere numeric**: Ring legend, metric tiles, live workout HUD — always tabular figures.
- **Full Dynamic Type**: Titles, body, footnotes scale through Apple's accessibility sizes; only the ring HUD and tab labels are layout-pinned.

## 4. Component Stylings

### Buttons

**Primary Button (Start Workout)**
- Shape: Rounded rectangle, 14pt corner radius (Apple's prominent button)
- Background: `#FA114F` (Move) — the brand accent as CTA
- Text: `#FFFFFF`, SF Pro 17pt weight 600
- Padding: 15pt vertical, 30pt horizontal (or full-width 50pt tall)
- Pressed: background `#D80E45` + scale 0.98
- Disabled: background `rgba(118,118,128,0.24)`, label tertiary

**Tinted Button (Add to Plan)**
- Background: `rgba(250,17,79,0.18)` (Move at 18%)
- Text: `#FF375F`, SF Pro 15pt weight 600
- Corner radius: 12pt
- Pressed: background `rgba(250,17,79,0.28)`

**Filled / Gray Button (Edit Goal)**
- Background: `rgba(118,118,128,0.24)` (system fill)
- Text: `#FFFFFF`, SF Pro 15pt weight 600
- Corner radius: 12pt
- Pressed: background `rgba(118,118,128,0.34)`

**Plain Button (See All / link)**
- Background: none
- Text: `#FF375F`, SF Pro 17pt weight 600

**Icon Button**
- 22pt SF Symbol in 44pt hit area
- Default `#FF375F` (interactive) or label-primary (neutral); pressed 60% opacity

### Core Atoms

**Activity Rings** (the hero atom)
- Three concentric circles, thick stroke (14pt at hero size, scales with diameter), round line cap
- Move outer (largest radius), Exercise middle, Stand inner
- Each ring: a full-circle track in its own color at 22% opacity + a progress arc in its full color, starting at -90° (12 o'clock), clockwise
- Overshoot: when a ring exceeds its goal it continues past 360° with a subtle layered look
- Glow: a soft outer glow in the ring color sells the "lit" feel on black

**Ring Legend Row**
- Label: SF Pro 12pt 700 uppercase in the ring's label-brightened color (Move `#FF375F`, Exercise `#92E82A`, Stand `#1EE4E1`)
- Value: SF Pro 19pt 800 tabular `#FFFFFF`
- Goal suffix: SF Pro 12pt 600 secondary-label ("/620 KCAL")

**Grouped Card**
- Background: `#1C1C1E` (system grouped), corner radius 14–18pt, no border, no drop shadow
- Internal padding: 14–22pt
- Stack of cards separated by 10–16pt gaps on true black

**Metric Tile**
- Grouped card, 14pt radius, 14pt padding
- Top row: 16pt tinted SF Symbol (in the relevant ring/metric color) + label SF Pro 12pt 600 secondary
- Value: SF Pro 22pt 800 tabular `#FFFFFF`
- Sub: SF Pro 12pt tertiary ("Avg 7,540")

**Fitness+ Card**
- 168pt wide, 200pt cinematic thumbnail, 16pt radius
- Frosted badge top-left: `rgba(0,0,0,0.4)` + blur, SF Pro 10pt 700 uppercase white
- Frosted play button bottom-left: 34pt circle, `rgba(255,255,255,0.22)` + blur, white triangle
- Below: type label SF Pro 12pt 700 uppercase `#FF375F`, title SF Pro 15pt 600 `#FFFFFF`, meta SF Pro 13pt secondary ("Bakari · Pop Anthems")

**Chip / Pill**
- Background: `rgba(118,118,128,0.24)` (selected: `#FF375F`)
- Text: SF Pro 13pt 600 (selected: white)
- Corner radius: 500pt

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: system `.thinMaterial` blur over true black, 0.5pt top separator `#38383A`
- Tabs (3): Summary, Fitness+, Sharing
- Icon: 24pt SF Symbol; **no tint pill**
- Active: glyph filled `#FF375F`, label `#FF375F`
- Inactive: glyph `tertiaryLabel`, label `tertiaryLabel`
- Labels: SF Pro 10pt 500, always shown

**Top (Summary)**
- Day eyebrow SF Pro 13pt 700 uppercase `#FF375F`
- Date SF Pro 32pt 800 label-primary
- Trailing: 32pt profile avatar circle
- Large-title "Summary" scrolls under a collapsing nav

**Section Header (in-page)**
- SF Pro 22pt 800 label-primary, 20pt top margin
- Trailing "See All" plain button in `#FF375F`

### Input Fields

**Search Bar (Fitness+ catalog)**
- Height: 36pt
- Background: `rgba(118,118,128,0.24)` (system search field)
- Corner radius: 10pt
- Leading magnifier 15pt tertiary-label
- Placeholder SF Pro 17pt tertiary-label

**Segmented Control (Day / Week / Month — ring history)**
- Track: `rgba(118,118,128,0.24)`, 8pt radius
- Selected segment: `#3A3A3C` fill with subtle elevation, white text
- Unselected: secondary-label text
- SF Pro 13pt 600

**Filter Pills (Fitness+ filter)**
- `rgba(118,118,128,0.24)` unselected, `#FF375F` selected
- SF Pro 13pt 600, 500pt radius

### Distinctive Components

**Activity Rings Hero**
- The three rings drawn large (110–130pt) on a grouped card with a legend
- Tappable → full-screen ring detail with hour-by-hour bar chart in the ring's color
- Animate: each ring sweeps from 0 to its value over ~1s ease-out, slightly staggered (Move, then Exercise, then Stand), with a soft haptic when a ring closes

**Ring Close Celebration**
- When a ring reaches 100%, a burst of small particles in the ring color + a success haptic + the ring's "closed" state (a subtle pulse)
- All-three-closed triggers a larger celebration

**Fitness+ Cinematic Shelf**
- Horizontally scrolling 200pt artwork cards with frosted-glass overlays
- Snap-scrolling, momentum; cards lift slightly on press (scale 0.98 → tap)

**In-Workout Ring HUD**
- During a Fitness+ session, the three rings overlay a corner of the trainer video and update live
- A "Burn Bar" can appear comparing your output to others' median
- Tappable metrics row (heart rate, active calories, time) in tabular SF Pro over the video with a subtle scrim

**Awards / Trophy Case**
- Grid of achievement medallions; earned ones full-color, unearned dimmed to `tertiaryLabel`
- Tapping a medallion flips it with a 3D rotation to show detail

**Sharing Activity Card**
- A friend row: avatar + name + their three mini-rings + a status line
- Your row pinned top; competition badges in Award Gold `#FFD60A`

**Trends Indicators**
- Up/down arrows beside a 90-day trend metric: green-up `#30D158` (improving) / `#FF453A` (declining), with the metric in tabular SF Pro

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Apple's 8pt-ish rhythm built from 4pt)
- Scale: 4, 8, 10, 12, 16, 20, 22, 28, 32, 44
- Grouped card internal padding: 14–22pt
- Card-to-card gap: 10–16pt
- Screen side inset: 16pt (Apple's standard readable margin)

### Grid & Container
- iPhone: 16pt side insets; metric tiles 2-up grid; Fitness+ shelf horizontal scroll
- iPad: content max-width centered; tiles 3–4-up; Fitness+ a multi-column grid
- Ring card: rings sit left, legend right (or rings centered with legend below on narrow)
- Fitness+ cards: fixed 168pt width, full-bleed thumbnail with rounded corners

### Whitespace Philosophy
- **The rings own the light**: the hero ring card gets generous space; everything else is calm and grouped so the rings stay the focal point
- **Apple grouped rhythm**: cards float on true black with consistent 10–16pt gaps — the standard iOS grouped-list feel
- **Cinematic content gets room**: Fitness+ artwork is large and uncluttered; chrome is thin
- **No decorative emptiness**: spacing is the iOS grouped system; energy comes from ring color + artwork, not empty space

### Border Radius Scale
- Square (0pt): full-bleed video, separators
- Standard (8pt): segmented control, search field
- Comfortable (12pt): tinted/filled buttons, small chips-as-cards
- Card (14pt): metric tiles, prominent buttons
- Grouped Hero (18pt): the ring hero card, large grouped cards
- Large (22pt): bottom sheets, modals
- Pill (500pt): filter pills, chips
- Circle (50%): rings, avatars, frosted play buttons

## 6. Depth & Elevation

Apple Fitness is **flat with material depth**. Depth is surface lightness + system blur materials, not custom drop shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | `#000000`, no shadow | App canvas |
| Grouped (Level 1) | `#1C1C1E`, no shadow | Ring card, metric tiles, grouped cards |
| Raised (Level 2) | `#2C2C2E` / `#3A3A3C` | Nested cells, pressed/selected control segments |
| Material (Level 3) | System `.thinMaterial` / `.regularMaterial` blur | Tab bar, frosted Fitness+ badges & play buttons, nav |
| Modal (Level 4) | `#1C1C1E` sheet + `.regularMaterial` + `rgba(0,0,0,0.4)` scrim | Bottom sheets, ring detail, share sheet |

**Shadow Philosophy**: On true black, drop shadows are invisible, so Apple uses none on content — depth is "lighter surface = closer." The only elevation tool beyond surface lightness is the **system blur material** (UIBlurEffect): the tab bar, the frosted badge/play overlays on Fitness+ artwork, and modal backgrounds all use Apple's vibrancy materials. The rings themselves get a soft colored *glow* (not a drop shadow) to read as "lit" on black.

### Motion
- **Ring sweep**: on Summary open, each ring animates from 0 → its value over ~1.0s ease-out, slightly staggered (Move → Exercise → Stand by ~80ms); overshoot rings continue past 360° with a layered wrap
- **Ring close**: at 100%, a particle burst in the ring color + a success haptic; the ring "pulses" once
- **All-rings-closed**: larger confetti-style celebration + stronger haptic
- **Card press**: grouped cards / Fitness+ cards scale 1.0 → 0.97 in 120ms, spring back on release
- **Tab switch**: cross-fade 200ms; active glyph swaps to filled instantly, tint tween 150ms
- **Fitness+ shelf**: momentum + snap horizontal scroll; parallax on thumbnails as they scroll
- **Sheet present**: ring-detail / share sheet slides up 350ms with the iOS sheet spring; scrim fades in
- **Live HUD (in-workout)**: rings update continuously with a smooth ~300ms ease as metrics change; heart-rate value ticks with tabular spacing
- **Award flip**: medallion 3D-rotates (Y axis) 500ms to reveal detail
- **Haptic**: light selection on tab/segment change; success notification on ring close; medium impact on Start Workout
- **Reduce Motion**: rings set their final value with a 250ms crossfade (no sweep), no particle burst (use a static "closed" check), no card scale, no parallax, awards crossfade instead of flip

## 7. Do's and Don'ts

### Do
- Use true black `#000000` as the iPhone canvas — the rings must be the brightest thing on screen
- Treat the three ring colors (Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`) as the immutable brand — never restyle them
- Draw each ring on a 22%-opacity track of its own color, thick stroke, round cap, start at 12 o'clock
- Give rings a soft colored glow (not a drop shadow) so they read as "lit" on black
- Use grouped system cards (`#1C1C1E`, 14–18pt radius) with depth from surface lightness
- Use Apple's label-opacity ramp (100% / 60% / 30%) for text, not fixed gray hexes
- Tint the active tab and links Move-pink `#FF375F` (the one chrome accent)
- Set numbers tabular so the ring legend and tiles never jitter
- Use system blur materials for the tab bar and frosted Fitness+ overlays
- Animate rings sweeping from 0 with a celebratory close + success haptic

### Don't
- Don't use charcoal as the base — Apple Fitness is *true* black `#000000` on iPhone
- Don't recolor the ring colors for theme, dark/light, or "brand harmony" — they are sacred
- Don't put rings on a neutral gray track — the track is the ring's own color at 22%
- Don't drop-shadow grouped cards — depth is surface lightness + blur materials
- Don't invent a bespoke type scale — follow Apple's text styles and Dynamic Type
- Don't use distinct gray hexes for secondary text — use white at 60% / 30% opacity
- Don't add a Material/iOS tint pill behind the active tab — use Apple's tint-only style
- Don't make Fitness+ artwork muted — it is cinematic, full-color, the visual reward
- Don't use proportional figures on the ring legend / metric tiles — tabular only
- Don't over-animate chrome — the rings carry the motion energy; chrome stays calm

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Rings ~110pt; metric tiles stay 2-up; Fitness+ card 150pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; collapsing large title |
| iPhone 15/16 Pro Max | 430pt | Rings ~140pt; Fitness+ card 184pt |
| iPad (portrait) | 768pt | Light grouped background option; tiles 3–4-up; Fitness+ multi-column grid |
| iPad (landscape) | 1024pt+ | Ring card + tiles in a sidebar layout; Fitness+ full catalog grid |

### Dynamic Type
- Scales: all titles, body, footnotes through Apple's accessibility sizes
- Fixed (layout-pinned): the ring legend numerals, in-workout HUD, tab labels, frosted badges — these live in tight live layouts
- Ring diameter scales with available width, not Dynamic Type

### Orientation
- Summary is portrait-first on iPhone; iPad supports both
- In-workout supports landscape: trainer video fills the frame, ring HUD overlays a corner
- Apple Watch mirror (where relevant) keeps the same ring semantics

### Touch Targets
- Tab bar icon: 24pt glyph, 49pt hit
- Ring hero card: full-card tap → ring detail, large area
- Metric tile: full-tile tap, ≥ 72pt tall
- Fitness+ card: full-card tap, ≥ 280pt tall
- Buttons: ≥ 44pt tall (primary 50pt full-width)

### Safe Area Handling
- Top: large title + day/date respect safe area and Dynamic Island
- Bottom: tab bar uses safe-area inset; in-workout HUD respects all edges over video
- Sides: 16pt readable content inset; Fitness+ thumbnails full-bleed within their card
- Materials extend edge-to-edge; content insets respect safe areas

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Grouped surface 1 / 2 / 3: `#1C1C1E` / `#2C2C2E` / `#3A3A3C`
- Separator: `#38383A`
- Move ring / label: `#FA114F` / `#FF375F`
- Exercise ring / bright: `#92E82A` / `#66FF00`
- Stand ring / bright: `#1EE4E1` / `#00F0FF`
- Ring track: ring color @ 22% opacity
- Chrome accent (active tab / links / CTA): `#FF375F` (pressed `#D80E45`)
- Fitness+ purple: `#C969E0`
- Label primary / secondary / tertiary: `#FFFFFF` / white@60% / white@30%
- Success / Error / Award gold: `#30D158` / `#FF453A` / `#FFD60A`
- System fill (neutral button): `rgba(118,118,128,0.24)`

### Example Component Prompts
- "Build the Apple Fitness Summary header in SwiftUI: a day eyebrow 'THURSDAY' (SF Pro 13pt 700 uppercase `#FF375F`), a date 'May 14' (SF Pro 32pt 800 label-primary) below it, and a 32pt circular profile avatar trailing. Below, a 'Summary' large title (SF Pro 40pt 800) that collapses into the nav on scroll. Canvas is true black `#000000`."

- "Create the Apple Fitness Activity rings: three concentric circles, round-capped, thick stroke (14pt at 130pt diameter). Move outer (`#FA114F`), Exercise middle (`#92E82A`), Stand inner (`#1EE4E1`). Each ring = a full-circle track in its own color at 22% opacity + a progress arc in full color starting at -90° clockwise. Add a soft outer glow in each ring's color. Animate each arc from 0 to its value over ~1.0s ease-out, staggered Move→Exercise→Stand by 80ms, with a success haptic when a ring reaches 100%."

- "Build the Apple Fitness ring legend: three rows. Each: an uppercase label in the ring's label-brightened color (Move `#FF375F`, Exercise `#92E82A`, Stand `#1EE4E1`, SF Pro 12pt 700), then the value in SF Pro 19pt 800 tabular white with a secondary-label goal suffix — '486 /620 KCAL', '38 /30 MIN', '9 /12 HR'. Lay it out beside the rings on a grouped `#1C1C1E` 18pt-radius card."

- "Build an Apple Fitness metric tile: a grouped `#1C1C1E` card, 14pt radius, 14pt padding. Top row a 16pt tinted SF Symbol (`chart.bar.fill` in `#FF375F`) + label 'Steps' (SF Pro 12pt 600 white@60%). Value '8,214' (SF Pro 22pt 800 tabular white). Sub 'Avg 7,540' (SF Pro 12pt white@30%). Lay four of these in a 2-column grid with 10pt gaps."

- "Render the Apple Fitness Fitness+ shelf: a horizontal snap-scroll of 168pt cards. Each card: a 200pt cinematic thumbnail (use a rich gradient placeholder), 16pt radius, a frosted badge top-left (`rgba(0,0,0,0.4)` + blur, 'NEW' SF Pro 10pt 700 uppercase white) and a 34pt frosted play button bottom-left (`rgba(255,255,255,0.22)` + blur, white triangle). Below: type label SF Pro 12pt 700 uppercase `#FF375F`, title '20 min HIIT' SF Pro 15pt 600 white, meta 'Bakari · Pop Anthems' SF Pro 13pt white@60%."

- "Build the Apple Fitness bottom tab bar: a 49pt bar using system `.thinMaterial` over true black with a 0.5pt `#38383A` top separator. Three tabs — Summary, Fitness+, Sharing — 24pt SF Symbols, no tint pill. Active glyph + label tinted `#FF375F`; inactive uses `tertiaryLabel`. Labels SF Pro 10pt 500 always shown."

### Iteration Guide
1. Canvas is **true black `#000000`** on iPhone — the three rings must be the brightest thing on screen
2. The brand **is** the three ring colors — Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1` — immutable, never restyled
3. Each ring sits on a **22%-opacity track of its own color**, thick round-capped stroke, starting at 12 o'clock, with a soft colored glow
4. Use **grouped system cards** (`#1C1C1E`, 14–18pt radius); depth is surface lightness + blur materials, never drop shadows
5. Text uses Apple's **label-opacity ramp** (100% / 60% / 30%), not fixed gray hexes; type is SF Pro per Apple's text styles
6. **Move-pink `#FF375F`** is the single chrome accent — active tab, links, primary CTA (pressed `#D80E45`)
7. Numbers are **tabular** — ring legend, metric tiles, live HUD never jitter
8. **Fitness+ artwork is cinematic and full-color** with frosted-glass overlays — the visual reward
9. Rings **sweep from 0** on open (staggered ~1s) and **celebrate on close** with a particle burst + success haptic
10. Chrome stays **calm and HIG-correct** — the rings and artwork carry the energy, not the framing
