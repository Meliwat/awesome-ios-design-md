# Design System Inspiration of Strava (iOS)

## 1. Visual Theme & Atmosphere

Strava is a sweat-stained newspaper for athletes. The canvas is white in light mode and a deep coal black in dark mode — neutral on purpose, because the headline of every screen is a map. A route polyline drawn over OpenStreetMap is the single most repeated visual in the app: in the activity feed (a small map snapshot on every card), on the activity detail (full-bleed map with overlay stats), on the segment leaderboard (a thin curve), and as the empty-state hero on the Maps tab. Strava's job is to make those polylines feel earned — they're rendered in Strava's signature pumpkin orange (`#FC4C02`), thicker than the underlying road network, with a luminance halo so the route reads against busy city streets and rural single-track alike.

Strava Orange is the only chromatic accent the brand needs. It's on the CTA buttons ("Record"), the active tab, the kudos glyph when filled, the segment-PR rank chip, the activity-type icon strip, and — most importantly — the route polyline. Everything else in the chrome is a careful gradient from `#FFFFFF` through warm grays (`#F5F4F2` surface) to true text black (`#0E0E0E`). There is no secondary brand color. The orange is the brand, the gradient grays are the substrate.

Typography is utilitarian by design — Strava ships with Maison Neue (the marketing/web face by Milieu Grotesque) for its desktop properties but inside the iOS app it leans on SF Pro because activity feeds, mile splits, and pace tables need the system's numeric weight stack. Numbers are tabular everywhere (`tnum`): pace columns, mile splits, distance, elapsed time, kudos counts. The hierarchy is more cardio than couture — bold display weights for headline stats (16:42 elapsed, 8.2 mi, 7:23 /mi pace), regular medium for activity titles, foggy grays for metadata. There's a clear sense that data is the brand: a Strava activity card is one map snapshot above a 3-up stat grid — distance, time, elevation — and that's what people screenshot to share.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / Coal Black canvas (`#0F0F0F`) dark — chromatically neutral so the map polyline pops
- Strava Orange (`#FC4C02`) — the only accent — on CTAs, active tab, polyline, kudos filled
- Route polyline rendered at 4pt stroke width with a 1pt `rgba(252,76,2,0.3)` halo for road-overlay legibility
- Activity feed cards: map snapshot at the top + 3-up stat grid (distance/time/pace) + kudos row at the bottom
- Kudos thumb glyph: outlined when un-given, filled Strava Orange when given; haptic on tap
- Segment leaderboard rows: rank chip (gold/silver/bronze for top 3), athlete avatar + name, time, gap to leader
- Big tabular numbers everywhere — pace, distance, elevation, splits — using SF Pro `monospacedDigit`
- Bottom tab bar: Home / Maps / Record / Groups / You — Record is a center action button in Orange
- SF Pro at weights 400/500/700/900 — heavy weights for hero stats, regular for body
- Cards are flat with hairline divider only — no shadows; the map photo provides all the visual hierarchy
- Activity-type icon strip — 18pt SF Symbols (figure.run, figure.outdoor.cycle, figure.pool.swim) tinted Strava Orange when matching activity

## 2. Color Palette & Roles

### Primary
- **Strava Orange** (`#FC4C02`): THE accent — Record CTA, active tab, route polyline, kudos filled, PR chip, follow-button text.
- **Orange Pressed** (`#D44002`): Active/pressed state on Strava Orange CTAs.
- **Orange Halo** (`rgba(252,76,2,0.3)`): The luminance ring drawn beneath the polyline to lift it off the map.

### Canvas, Surfaces & Dividers (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary canvas — feed, profile, settings.
- **Surface Warm** (`#F5F4F2`): Section backgrounds, search field fill, profile header backdrop — a subtle warm gray, not pure neutral.
- **Surface Cool** (`#F0F0F0`): Activity-card secondary surfaces, the stat-grid divider zones.
- **Divider** (`#E5E5E5`): Hairline row dividers between feed cards, settings rows.
- **Map Tile Default** (`#E8E5DD`): The OpenStreetMap-derived base tile color (Strava's custom-styled raster) — warm parchment, never gray.

### Text (Light Mode)
- **Ink Primary** (`#0E0E0E`): Primary text — activity titles, hero stats, athlete names.
- **Ink Secondary** (`#666666`): Secondary text — timestamps, "Hosted by ...", "5 followers", metadata.
- **Ink Tertiary** (`#9A9A9A`): Tertiary — placeholders, disabled labels, "no kudos yet".
- **Inverted White** (`#FFFFFF`): Text on dark surfaces (the Record button, the bottom bar).

### Dark Mode
Strava's dark mode is deeply considered — coal black so the orange polyline glows.
- **Coal Black Canvas** (`#0F0F0F`): Primary dark canvas.
- **Dark Surface 1** (`#1A1A1A`): Card backgrounds, profile header on dark.
- **Dark Surface 2** (`#262626`): Pressed states, chip fills, search field fill.
- **Dark Divider** (`#2A2A2A`): Hairlines.
- **Dark Text Primary** (`#F2F2F2`): Activity titles.
- **Dark Text Secondary** (`#A0A0A0`): Metadata.
- **Strava Orange (dark)** (`#FC4C02`): Identical — orange reads beautifully on coal.
- **Map Tile (dark)** (`#1B1B1B`): Strava's custom dark map style — deep charcoal with white street network.

### Achievement & Rank Colors
- **PR Gold** (`#F5C24A`): Personal Record (PR) flag, top-1 on a segment.
- **2nd Silver** (`#C6C6C6`): Top-2 on a segment.
- **3rd Bronze** (`#CD7F32`): Top-3 on a segment.
- **KOM/QOM Crown** (`#FFD700`): King/Queen of the Mountain segment crown (slightly more gold than PR).

### Semantic
- **Success Green** (`#22C55E`): Activity uploaded, sync complete.
- **Heart Red** (`#E74C3C`): Heart rate zone hi (used on HR charts).
- **Pace Blue** (`#3B82F6`): Pace chart line on graph overlays.
- **Elevation Brown** (`#8B6F47`): Elevation chart fill.
- **Warning Amber** (`#F59E0B`): Activity not synced, GPS signal weak.
- **Error Red** (`#EF4444`): Failed sync, payment failed (Subscription).

### Activity-Type Tinting
When the activity card is highlighted in the feed (e.g., a friend's run), the activity-type icon takes the Strava Orange. Otherwise icons are `#666666` ink-secondary. Activity types have their own ghost colors but Strava deliberately keeps them muted — orange remains the focal point.

## 3. Typography Rules

### Font Family
- **Primary (iOS app)**: `SF Pro Display` (titles 20pt+) / `SF Pro Text` (body 17pt and below)
- **Marketing / web**: `Maison Neue` by Milieu Grotesque (Book, Medium, Bold)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Maison Neue', 'Helvetica Neue', Arial, sans-serif`
- **Web/marketing Google Fonts substitute**: `Inter` at -0.4 letter-spacing approximates Maison Neue; or `IBM Plex Sans` for a more grotesque feel
- **Numeric weight**: SF Pro `monospacedDigit()` everywhere there's a stat — pace, distance, elapsed time, splits, kudos count, follower count

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Stat (Activity Detail) | SF Pro Display | 44pt | 900 (Black) | 1.0 | -0.6pt | "8.2" mi, "1:14:23" elapsed — the big number |
| Hero Stat Unit | SF Pro Text | 13pt | 600 | 1.0 | 0.5pt | "mi", "MIN/MI" — UPPERCASE under the hero number |
| Large Nav Title | SF Pro Display | 28pt | 700 (Bold) | 1.1 | -0.4pt | "Home", "Activity", "Maps" |
| Activity Title (Feed Card) | SF Pro Text | 17pt | 600 (Semibold) | 1.2 | -0.2pt | "Tuesday Morning Run" |
| Athlete Name (Feed Card Header) | SF Pro Text | 15pt | 600 | 1.2 | -0.1pt | The author of the activity |
| Section Header | SF Pro Text | 13pt | 700 | 1.2 | 0.5pt | UPPERCASE — "ACHIEVEMENTS", "SPLITS" |
| Body | SF Pro Text | 15pt | 400 (Regular) | 1.4 | -0.2pt | Activity description, comments |
| Body Small | SF Pro Text | 13pt | 400 | 1.4 | -0.1pt | Activity timestamp, "5 minutes ago" |
| Stat Number (Feed Card) | SF Pro Text | 17pt | 700 | 1.1 | 0pt | The 3-up grid: "8.2", "1:14:23", "9:03" — tabular |
| Stat Label | SF Pro Text | 11pt | 600 | 1.2 | 0.6pt | UPPERCASE — "DISTANCE", "TIME", "PACE" |
| Kudos Count | SF Pro Text | 13pt | 600 | 1.0 | 0pt | "27" — tabular, after the thumb glyph |
| Metadata | SF Pro Text | 13pt | 400 | 1.3 | -0.1pt | "Boston, MA · 2 hours ago" |
| Button (Primary) | SF Pro Text | 17pt | 700 | 1.0 | -0.2pt | "Record", "Follow", "Save" |
| Button (Secondary) | SF Pro Text | 15pt | 600 | 1.0 | -0.1pt | "Give Kudos", "Comment" |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.2pt | Bottom tab labels |
| Segment Rank | SF Pro Text | 13pt | 800 | 1.0 | 0pt | "1", "2", "3" inside the rank chip |
| Caption | SF Pro Text | 11pt | 400 | 1.3 | 0.1pt | "as of 9:42 AM", "GPS accuracy ±3m" |

### Principles
- **Weights concentrated at 400 / 600 / 700 / 900**: Regular for body, Semibold for activity titles & metadata labels, Bold for stats and section headers, Black for the hero stats on activity detail.
- **`monospacedDigit()` on every number**: pace, time, distance, splits, kudos count, follower count — Strava's column alignment depends on it.
- **Stat labels are 11pt UPPERCASE Semibold with 0.6pt tracking**: "DISTANCE / TIME / PACE / ELEV". Always.
- **Ink Primary `#0E0E0E` is darker than typical body**: Strava intentionally pushes contrast — the map gets light, the text gets dark.
- **Dynamic Type respected on body and titles**; FIXED on hero stats and stat labels because the 3-up grid alignment breaks.
- **Tabular numerals over comma columns**: when you have a splits table, every digit is the same width.

## 4. Component Stylings

### Activity Feed Card (the hero component)

**Card Structure**
- Full content width (viewport - 16pt horizontal margins)
- Top: 56pt header strip — 40pt circular avatar + athlete name (15pt Semibold) + activity-type icon + timestamp (13pt Regular `#666666`)
- Activity title row: 17pt Semibold `#0E0E0E`, full-width, 1-line truncate (e.g. "Tuesday Morning Run")
- Optional description: 15pt Regular `#0E0E0E`, max 3 lines, "Read more" link
- Map snapshot: full-width, aspect ratio 16:9, corner radius 6pt, route polyline rendered at 4pt stroke in `#FC4C02` with `rgba(252,76,2,0.3)` halo
- 3-up stat grid below the map: distance / time / pace (or elevation for rides) — each cell is 1/3 width, vertical-aligned, top label 11pt UPPERCASE Semibold `#666666` + 17pt Bold value `#0E0E0E`
- Achievements row (optional, after stats): horizontal row of badges — "1 PR" + "3 Personal Bests" + "KOM Attempt" in 13pt Semibold tinted Orange
- Kudos + Comments row: bottom of card — 32pt thumb-up glyph + count + 24pt speech-bubble glyph + count, left-aligned; "View Activity" link right-aligned in 13pt Semibold Orange
- Divider between cards: 8pt vertical gap with no hairline (the next card's white background acts as the divider)
- Card background: `#FFFFFF` (light) / `#1A1A1A` (dark); no shadow, no border

### Map Snapshot (the route polyline)

**Map Tile Style**
- Strava-customized OpenStreetMap raster — warm parchment `#E8E5DD` base with bone-colored road network and lighter buildings
- Dark mode tile: charcoal `#1B1B1B` with white roads (`#FFFFFF` @ 70%) and pale building footprints

**Route Polyline**
- Stroke: 4pt solid `#FC4C02`
- Halo (drawn beneath the stroke): 1pt offset on every side, `rgba(252, 76, 2, 0.3)` — this is what makes the polyline read on busy streets
- Stroke caps: round
- Join: round (no mitered corners on switchbacks)
- For long activities (>10mi), the polyline thins to 3pt to avoid dominating the snapshot

**Start/End Markers**
- 12pt circle filled white with 2pt `#FC4C02` outline — at the route start
- 12pt filled `#FC4C02` circle with 2pt white outline — at the route end
- Tip: in a feed snapshot they overlap if it's a loop, which is fine

### 3-Up Stat Grid (below the map on every activity card)

**Layout**
- Full width, evenly distributed 3 columns
- Each cell: vertical stack of label (top) + value (bottom)
- 16pt vertical padding, 0pt horizontal gap (cells abut)
- 1pt `#F0F0F0` vertical divider between cells (only in light mode; absent in dark)

**Cell Content**
- Label: 11pt UPPERCASE Semibold `#666666`, 0.6pt tracking — "DISTANCE", "TIME", "PACE" or "ELEV"
- Value: 17pt Bold `#0E0E0E`, tabular — "8.2", "1:14:23", "9:03"
- For rides, swap order: "DISTANCE / ELEV GAIN / AVG SPEED"
- For swims: "DISTANCE / TIME / PACE/100"
- The label-to-value gap is 4pt fixed

### Kudos & Comments Row

**Kudos Thumb**
- Container: 36pt × 36pt circular tap area
- Glyph: `hand.thumbsup` (outlined) when un-given / `hand.thumbsup.fill` (Strava Orange) when given
- Default color: `#666666` outlined
- Given color: `#FC4C02` filled
- Animation on tap: scale 1.0 → 1.3 → 1.0 over 300ms spring + emit confetti at 8 small Orange particles fading up + `.impactOccurred(.medium)` haptic
- Count: 13pt Semibold `#0E0E0E` to the right of the glyph

**Comment Bubble**
- Glyph: `bubble.left` outlined SF Symbol, 18pt, `#666666`
- Count: 13pt Regular `#666666`

**View Activity Link**
- "View Activity" in 13pt Semibold `#FC4C02`, right-aligned
- 44pt tap area extending across the full right edge

### Achievement Badge (on a feed card if any PRs occurred)

**Format**
- Pill-shape, full pill radius (500pt), 24pt height
- Inline icon + text — 14pt `figure.run.circle.fill` Orange + "1 PR" in 13pt Semibold `#0E0E0E`
- Background: `rgba(252,76,2,0.08)` light tint, transparent in dark with `rgba(252,76,2,0.15)`
- Padding: 4pt vertical, 10pt horizontal
- Horizontal scroll if more than 3 badges fit

### Segment Leaderboard Row

**Layout**
- Height: 56pt
- Left: 32pt × 32pt rank chip — circular, filled Gold for #1 / Silver for #2 / Bronze for #3 / `#F0F0F0` for the rest, with the rank number in 13pt Black white (or `#0E0E0E` for #4+)
- Avatar: 36pt circular, immediately right of rank chip
- Middle stacked: athlete name 15pt Semibold `#0E0E0E` above date 11pt Regular `#666666`
- Right stacked: segment time 17pt Bold `#0E0E0E` tabular above gap to leader 11pt Regular `#666666` ("+0:12")
- Background: alternating `#FFFFFF` / `#FAFAFA` rows (zebra)
- Divider: 0.5pt `#E5E5E5` hairline at bottom
- Tap haptic: `.selectionChanged()`

### Record Button (the center tab — Strava's main CTA)

**Format**
- Tab bar center action — protrudes 8pt above the bar's top edge
- Circle: 56pt × 56pt, `#FC4C02` background
- Glyph: 22pt `record.circle` or `play.fill` SF Symbol, white
- Shadow: `rgba(252,76,2,0.4) 0 8px 16px`
- Tap: scales to 0.95, immediately opens Record sheet with map and activity-type chooser
- Haptic: `.impactOccurred(.heavy)` — Record is a serious commitment, the heaviest haptic in the app

### Buttons

**Primary Pill CTA (Record / Follow / Save)**
- Background: `#FC4C02`
- Text: `#FFFFFF`, SF Pro Text 17pt Semibold
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 8pt (rectangular pill, not fully rounded — Strava uses semi-soft corners)
- Height: 50pt minimum
- Pressed: background `#D44002`, scale 0.98

**Secondary Outline Button**
- Background: transparent
- Border: 1.5pt `#0E0E0E`
- Text: `#0E0E0E`, SF Pro Text 15pt Semibold
- Corner radius: 8pt

**Following Button (after following an athlete)**
- Background: `#F0F0F0` (light) / `#262626` (dark)
- Text: `#0E0E0E` (light) / `#F2F2F2` (dark), 15pt Semibold
- Border: none
- Visually demoted compared to "Follow" — the active state is the orange

**Text Link**
- 13pt Semibold `#FC4C02`, no underline, 44pt hit area
- Used heavily for "View Activity", "See All", "More" affordances

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area (slightly taller than HIG default because of the center Record button)
- Background: `#FFFFFF` (light) / `#0F0F0F` (dark) with 0.5pt top divider; no blur
- Tabs (5): Home / Maps / **Record** (center) / Groups / You
- Standard tab icon: 24pt outlined SF Symbol, `#666666` inactive / `#FC4C02` active
- Center Record button: as described above — protrudes 8pt above the bar
- Tab labels: 10pt Semibold, visible on all tabs except Record (which is icon-only)

**Top App Bar (per screen)**
- Height: 44pt + safe area
- Background: `#FFFFFF` / `#0F0F0F`
- Left: 28pt nav title (SF Pro Display 28pt Bold), or back chevron + title
- Right: action icons — search, filter, settings — 22pt SF Symbols `#0E0E0E`

### Input Fields

**Text Field (Activity Title, Description, Comment)**
- Background: `#F0F0F0` (light) / `#262626` (dark)
- Border: none
- Corner radius: 8pt
- Padding: 12pt vertical, 14pt horizontal
- Text: SF Pro Text 15pt Regular `#0E0E0E`
- Placeholder: `#9A9A9A`

**Search Field**
- Background: `#F5F4F2`
- Border: none
- Corner radius: 10pt
- Height: 36pt
- Leading: 14pt `magnifyingglass` `#666666`
- Text: SF Pro Text 15pt Regular

### Distinctive Components

**Pace Chart Overlay (Activity Detail bottom sheet)**
- A small graph card with the pace-vs-time line in Pace Blue `#3B82F6`, the elevation in Elevation Brown `#8B6F47` filled underneath, and the heart-rate trace in Heart Red `#E74C3C`
- X-axis: time, 5 evenly-spaced ticks
- Y-axis: pace inverted (slower at the bottom, faster at the top) — runners think in pace, not speed
- Tap-and-drag on the chart reveals a vertical scrubber with the current value badge

**Splits Table**
- Mile or kilometer rows, scroll vertically
- Each row: mile number (13pt Regular `#666666`) + split time (17pt Bold `#0E0E0E` tabular) + delta vs avg (13pt Semibold Orange if faster, Heart Red if slower, with a tiny `arrow.up`/`arrow.down`)
- Bar visualization: a tiny horizontal bar inline with the time, scaling proportionally to that split's value

**Match Cup (Segment Notification)**
- A 24pt × 28pt trophy glyph `trophy.fill` in PR Gold (`#F5C24A`) for PRs, KOM Crown `#FFD700` for KOM/QOM
- Appears in feed-card achievement strip and the activity-detail header
- Animates with a subtle 600ms scale 1.0 → 1.1 → 1.0 pulse when first viewed

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard horizontal margin: 16pt on most screens, 24pt on activity-detail hero
- Feed card vertical gap: 8pt (cards almost touch — the visual rhythm is map snapshots)
- Section vertical gap inside activity detail: 32pt between major sections

### Grid & Container
- Content width: full minus 16pt horizontal margins
- Feed cards: always full width, stacked vertically — no grid, no carousel
- Profile activity grid: 3-col 1:1 thumbnail grid showing latest 9 activities (Instagram-like)
- Splits table: full-width data table, no card wrapper

### Whitespace Philosophy
- **Map is the breathing room**: the 16:9 map snapshot has a high visual density, so the rest of the card sits tight against it
- **Stats compress, titles extend**: 11pt label + 17pt value is a 32pt-tall stat cell; activity titles can wrap to 2 lines
- **No card shadows**: cards are visually separated by their map snapshots + 8pt of canvas in between

### Border Radius Scale
- Square (0pt): map polyline halo (it's stroked, no fill)
- Standard (6pt): map snapshot corner radius
- Soft (8pt): primary CTA, secondary outline, text fields
- Comfortable (10pt): search field, segment leaderboard row group
- Pill (500pt): achievement badges, follow chip
- Circle (50%): avatars, rank chips, kudos thumb tap area

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed cards, list rows, canvas — Strava is almost shadowless |
| Inline (Level 0.5) | 0.5pt `#E5E5E5` hairline | Section dividers, splits-table row separators |
| Floating (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Search field on map screens, segment popovers |
| Record Button (Level 2) | `rgba(252,76,2,0.4) 0 8px 16px` | The center Record tab button — Strava's only true elevation |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Bottom sheets, activity detail modal |

**Shadow Philosophy**: Strava is essentially flat. The only shadow that matters is the colored shadow under the Record button — a glowing Orange aura that signals "this is the moment." Everything else relies on hairlines, the map's natural visual density, or no separation at all. Cards stack with 8pt of canvas between them and that's it.

### Motion
- **Kudos tap**: scale 1.0 → 1.3 → 1.0 spring (damping 0.6, 300ms), color outlined → filled, emit 8 confetti particles fading up over 600ms, haptic `.impactOccurred(.medium)`
- **Record button tap**: scale 0.95 instant + heavy haptic `.impactOccurred(.heavy)`, then sheet rise from bottom in 350ms `.easeOut`
- **Activity card tap**: scale 0.99 briefly (very subtle), then push to activity detail in 280ms standard nav transition
- **Tab switch**: `.selectionChanged()` haptic, icon transition from outlined to filled over 200ms
- **Pace chart scrub**: real-time, no haptic — chart updates immediately, value badge follows finger
- **Segment notification slide-in (top toast)**: 400ms spring from top, lingers 3s, slides out
- **Pull-to-refresh**: standard system refresh control tinted Strava Orange

## 7. Do's and Don'ts

### Do
- Use `#FC4C02` as the SINGLE accent — Record button, active tab, polyline, kudos filled, achievement chips
- Render the route polyline at 4pt stroke with the `rgba(252,76,2,0.3)` halo — this is the signature visual
- Use tabular numerals on EVERY number (pace, time, distance, splits, kudos count) via `monospacedDigit()`
- Use the 3-up stat grid pattern on every activity card — never break this pattern for "more info"
- Place stat labels in 11pt UPPERCASE Semibold with 0.6pt tracking — this is the Strava data voice
- Use SF Pro Display Black weight 900 for the hero stats on activity detail — these are the trophy numbers
- Keep cards flat with no shadow — let the map snapshot do the visual work
- Make the Record button protrude 8pt above the tab bar with its orange glow shadow
- Animate kudos with the 1.3x scale bounce + confetti — it's a celebratory micro-interaction
- Use the heavy haptic `.impactOccurred(.heavy)` on Record only — it's the most serious tap in the app
- In dark mode use coal `#0F0F0F` not pure black — the polyline reads better on slightly-warm dark

### Don't
- Don't introduce a secondary brand color — Strava is monochromatically Orange, that's the discipline
- Don't render the polyline without the halo — busy city maps will swallow a bare orange stroke
- Don't use proportional digits in stats — column alignment will break and Strava users notice
- Don't add card drop shadows — the visual hierarchy comes from the map snapshot, not elevation
- Don't replace the 3-up stat grid with a list — it must be a horizontal trio
- Don't use system-yellow for PRs — Strava's PR gold is `#F5C24A`, slightly cooler and warmer at once
- Don't use the heavy haptic on kudos — that's reserved for Record; kudos is `.medium`
- Don't hide the Record button on screens deep in the nav — it persists across all tabs in the bottom bar
- Don't reformat times as "1h 14m 23s" — Strava uses "1:14:23" tabular with `monospacedDigit()`
- Don't make activity titles span more than 2 lines on a card — truncate with ellipsis

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Map snapshot maintains 16:9; stat grid compresses to 11pt label / 16pt value |
| iPhone 13/14/15 | 390pt | Standard layout, all stats inline |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected — top app bar sits below it |
| iPhone 15/16 Pro Max | 430pt | Larger map snapshot, optional 4-up stat grid (adds elevation) |
| iPad | 768pt+ | 2-col feed grid; activity detail uses split view (map left 60%, stats/splits right 40%) |

### Dynamic Type
- Activity titles and body: full scale
- Hero stats (44pt Black): clamp at 56pt to prevent overflow
- Stat labels and stat values in 3-up grid: FIXED at 11pt/17pt — layout breaks if they scale
- Tab labels and rank chips: FIXED

### Orientation
- Portrait-locked everywhere except:
- Activity detail map can full-screen and rotate to landscape
- Splits chart and pace chart rotate

### Touch Targets
- Kudos thumb: 36pt visual on a 44pt tap area
- Comment glyph: 36pt visual on a 44pt tap area
- Record button: 56pt visual on a 64pt tap area
- Segment row: full 56pt height tap

### Safe Area Handling
- Top: safe area honored — large title starts at safe-area top + 16pt
- Bottom: tab bar with Record button respects home indicator; the Record button's shadow extends below the indicator into the safe area
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Surface Warm: `#F5F4F2`
- Surface Cool: `#F0F0F0`
- Divider: `#E5E5E5`
- Ink Primary: `#0E0E0E`
- Ink Secondary: `#666666`
- Strava Orange: `#FC4C02`
- Orange Pressed: `#D44002`
- Orange Halo: `rgba(252,76,2,0.3)`
- PR Gold: `#F5C24A`
- Map Tile (light): `#E8E5DD`
- Coal Black (dark canvas): `#0F0F0F`
- Map Tile (dark): `#1B1B1B`
- Success Green: `#22C55E`
- Heart Red: `#E74C3C`
- Pace Blue: `#3B82F6`

### Example Component Prompts
- "Build a Strava activity feed card: full-width minus 16pt margins, no shadow, no border. Header row 56pt with a 40pt circular avatar, then athlete name 'Casey Reardon' in SF Pro Text 15pt Semibold `#0E0E0E`, then timestamp '2 hours ago' in SF Pro Text 13pt Regular `#666666`. Below: activity title 'Tuesday Morning Run' in SF Pro Text 17pt Semibold. A 16:9 map snapshot below with corner radius 6pt — the route polyline rendered at 4pt stroke `#FC4C02` with a 1pt halo at `rgba(252,76,2,0.3)`. Below the map a 3-up stat grid: 'DISTANCE 8.2', 'TIME 1:14:23', 'PACE 9:03 /mi' — labels 11pt UPPERCASE Semibold `#666666` with 0.6pt tracking, values 17pt Bold `#0E0E0E` tabular. At the bottom, a kudos thumb glyph + count, a comment bubble + count, and a 'View Activity' link in `#FC4C02`."
- "Render the Strava Record button: 56pt × 56pt circle, `#FC4C02` background, 22pt white play/record glyph centered, sitting in the center of a 5-tab bottom bar but protruding 8pt above the bar's top edge. Shadow: `rgba(252,76,2,0.4) 0 8px 16px` — a colored glow. On tap: scale 0.95 + haptic `.impactOccurred(.heavy)`."
- "Build a Strava segment leaderboard row: 56pt tall. Leading 32pt circular rank chip — gold (#F5C24A) for #1, silver (#C6C6C6) for #2, bronze (#CD7F32) for #3, neutral (#F0F0F0) for the rest — with the rank number in 13pt Black weight 800 white (or `#0E0E0E` for non-podium). Then a 36pt circular athlete avatar. Middle stacked: athlete name in 15pt Semibold `#0E0E0E` + date in 11pt Regular `#666666`. Right stacked: segment time in 17pt Bold `#0E0E0E` tabular + gap to leader in 11pt Regular `#666666`. Bottom 0.5pt `#E5E5E5` separator."
- "Design the kudos thumb interaction: outlined `hand.thumbsup` SF Symbol 24pt `#666666` when un-given, filled `hand.thumbsup.fill` `#FC4C02` when given. On tap: scale 1.0 → 1.3 → 1.0 over 300ms spring (damping 0.6), emit 8 small Orange particles fading up over 600ms, haptic `.impactOccurred(.medium)`. Count text 13pt Semibold `#0E0E0E` to the right of the glyph."
- "Render a Strava activity-detail hero: full-bleed map on top occupying 40% of screen height with the route polyline. Below, a horizontal 3-up grid of hero stats — '8.2 MI', '1:14:23', '9:03 /MI' — each cell using SF Pro Display 44pt Black `#0E0E0E` for the value with `monospacedDigit()` and SF Pro Text 13pt Semibold UPPERCASE `#666666` for the unit. Below that a sticky bottom action bar with 'Give Kudos' + 'Comment' + 'Share' buttons in outline style."

### Iteration Guide
1. The only brand accent is `#FC4C02` Strava Orange — never introduce a secondary
2. Every route polyline is 4pt with the `rgba(252,76,2,0.3)` halo
3. Every number is tabular — `monospacedDigit()` is mandatory
4. The 3-up stat grid is the universal activity-card pattern — never break it
5. Stat labels are 11pt UPPERCASE Semibold with 0.6pt tracking — Strava's data voice
6. Hero stats on activity detail are SF Pro Display 44pt Black — these are the trophy numbers
7. The Record button is the only elevated element — it protrudes 8pt and glows orange
8. Cards are flat, no shadow — let the map snapshot do the visual work
9. Dark mode is coal `#0F0F0F` not pure black — the polyline reads better
10. Haptic hierarchy: heavy on Record, medium on Kudos, selection on tab switches
