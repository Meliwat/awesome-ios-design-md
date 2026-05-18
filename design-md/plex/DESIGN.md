# Design System Inspiration of Plex (iOS)

## 1. Visual Theme & Atmosphere

Plex's iOS app is a calm, personal home cinema. Unlike commercial streamers it is not selling a catalog — it is presenting *your* media: the movies, shows, music, and photos living on a server you control. The design reflects that ownership with a quiet, neutral confidence. The canvas is a **cool charcoal** (`#1F2326`) — deliberately *not* true black. This is a defining choice: a slightly-blue charcoal makes poster art read as warm and rich against it, keeps long browsing sessions easy on the eyes, and signals "calm utility" rather than "loud entertainment". Chrome is restrained and even — flat surfaces, subtle dividers, generous spacing, and one accent color used sparingly.

That one accent is **Plex Yellow** (`#E5A00D`) — a warm amber-gold. It is the brand's entire color identity and it is used with strict discipline: the primary "Play" button, the active bottom-tab icon and label, the On Deck progress bar, the small "unwatched" corner dot on library posters, and the check mark on the selected server. Critically, **on filled yellow surfaces the text/icon is dark ink** (`#1A1304`), never white — yellow-on-white fails contrast and looks cheap; the dark-on-gold pairing is the Plex signature and must be preserved everywhere the yellow fills a shape.

The signature interaction surface is the **Home screen**: a compact **server picker** pill at the top (a status dot + server name + chevron — because Plex users routinely have more than one server and need to know which is online), then the **On Deck rail** — a horizontally scrolling row of wide 16:9 resume tiles, each with a centered play overlay and a yellow progress bar pinned to the bottom showing exactly where you stopped — followed by **library poster grids** (Movies, TV Shows, Music) in a clean 3-column 2:3 grid, where unwatched items carry a single small yellow corner dot. The server picker sheet (a list of servers with online/offline status dots and a yellow check on the active one) is the most Plex-specific component — no consumer streamer has it, because no consumer streamer is server-driven.

Typography is **Inter** — a neutral, highly legible UI sans that matches Plex's "calm utility" tone. Hierarchy is functional rather than expressive: clear screen titles, medium section headers, light metadata. Weights concentrate at 400 (body/meta), 600 (card titles, server names), 700 (section headers), and 800 (screen/hero titles). The type never shouts; it organizes. On iOS, Inter falls back to SF Pro.

**Key Characteristics:**
- Cool charcoal canvas (`#1F2326`) — intentionally NOT true black; calm, neutral, easy on the eyes
- Single accent — Plex Yellow (`#E5A00D`) always paired with dark ink (`#1A1304`) on fills, never white-on-yellow
- Server picker pill + sheet — status dot + name + chevron; the defining server-driven affordance
- On Deck rail — wide 16:9 resume tiles with a centered play overlay and a yellow progress bar
- Library poster grid — clean 3-col 2:3 grid; unwatched items carry one small yellow corner dot
- Multi-server status — online (green `#4CAF50`) / offline (gray `#6B7075`) dots; yellow check on the selected server
- Inter typography — neutral, legible, organizes rather than shouts
- Flat surfaces, even spacing, subtle `#34393D` dividers — no heavy shadows or gradients
- Yellow appears only in ~5 roles: Play button, active tab, On Deck progress, unwatched dot, selected-server check
- Five-tab bottom bar: Home / Library / Discover / Live TV / You
- Dark-first; there is no true-black mode and no light mode in the core media experience

## 2. Color Palette & Roles

### Primary (Interactive)
- **Plex Yellow** (`#E5A00D`): The single brand accent — primary "Play" button fill, active tab icon + label, On Deck progress bar, unwatched corner dot, selected-server check.
- **Yellow Pressed** (`#C98A09`): Pressed state of the yellow Play button.
- **Yellow Ink** (`#1A1304`): Text/icon color on top of any yellow fill — the mandatory dark-on-gold pairing (never white).

### Canvas & Surfaces (Dark — the primary mode)
- **Charcoal Canvas** (`#1F2326`): Primary canvas — cool charcoal, deliberately not true black.
- **Surface 1** (`#282C30`): Server-picker pill, the sheet background, elevated cards.
- **Surface 2** (`#32373B`): List rows, ghost-button fill, search field, unselected server rows.
- **Surface 3** (`#3C4146`): Selected/pressed row, progress-track background, chips.
- **Divider** (`#34393D`): Hairline dividers between rows and sections.

> Plex's core media experience is **dark-first**. A pure-white light mode is not part of the signature; the cool charcoal is the identity. (Settings screens may follow system appearance, but library/browse stays dark.)

### Text
- **Text Primary** (`#F2F3F4`): Screen titles, card titles, server names — near-white, slightly cool.
- **Text Secondary** (`#9BA0A4`): Metadata lines (S2 · E5 · 22 min left), captions, inactive tab labels.
- **Text Tertiary** (`#6B7075`): Disabled actions, very low-emphasis text, offline timestamps.
- **Text On-Yellow** (`#1A1304`): The dark ink used for any label on a yellow fill.

### Brand & Status Accents
| Role | Color | Usage |
|------|-------|-------|
| Plex Yellow | `#E5A00D` | Play button, active tab, On Deck progress, unwatched dot, selected-server check |
| Yellow Pressed | `#C98A09` | Play button pressed |
| Yellow Ink | `#1A1304` | Text/icon on any yellow fill (mandatory) |
| Online Green | `#4CAF50` | Server "online" status dot |
| Offline Gray | `#6B7075` | Server "offline" status dot |

### Semantic
- **Error Red** (`#E5484D`): Server-unreachable banner, playback error, destructive confirm.
- **Success** (`#4CAF50`): "Marked as watched" toast, sync-complete (reuses online green).
- **Warning Amber** (`#E5A00D`): Transcoding / bandwidth warning (reuses Plex Yellow, with dark ink).
- **Scrim**: `rgba(0,0,0,0.55)` circular backing behind the centered On-Deck play glyph.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, 2020, SIL OFL) — neutral, highly legible humanist UI sans with excellent small-size hinting; matches Plex's calm-utility tone.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: When Inter is not bundled, falls back to SF Pro; weights map 1:1.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.4pt | "Library", "Home" large nav titles |
| Hero Title | Inter | 26pt | 800 | 1.2 | -0.3pt | Detail-screen media title |
| Section Header | Inter | 22pt | 700 | 1.2 | -0.2pt | "On Deck", "Continue Watching", library names |
| Subsection | Inter | 18pt | 700 | 1.25 | -0.1pt | "Recently Added", "Extras" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Summary paragraph, About text |
| Card Title | Inter | 15pt | 600 | 1.3 | 0pt | On Deck / poster title |
| Meta Line | Inter | 14pt | 400 | 1.4 | 0pt | "S2 · E5 · 22 min left", "2023 · PG-13" |
| Button Label | Inter | 16pt | 700 | 1.0 | 0pt | "Play", "Mark as Watched" |
| Server Name | Inter | 14pt | 600 | 1.3 | 0pt | Server-picker row name |
| Server Meta | Inter | 12pt | 400 | 1.3 | 0pt | "Online · 2,481 items · LAN" |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom-tab labels |
| Caption / Tech | Inter | 12pt | 600 | 1.3 | 0.2pt | "1080p · 5.1", quality/codec chips |

### Principles
- **Functional, not expressive**: Type organizes the media library — it never carries brand personality the way the yellow does.
- **Weight for structure**: 800 for screen/hero titles, 700 for section headers, 600 for card/server names, 400 for body/meta.
- **Metadata is secondary gray** (`#9BA0A4`) — episode/year/runtime never compete with the title.
- **Tech chips are small + tracked**: quality/codec captions (1080p · 5.1) use 12pt weight 600 with slight positive tracking.
- **Calm rhythm**: generous line-height (1.4–1.5 on body/meta) reinforces the "easy on the eyes" feel.
- **Dynamic Type**: titles, body, meta scale; tab labels, tech chips, progress-bar geometry, status dots stay FIXED.

## 4. Component Stylings

### Buttons

**Primary Button ("Play")**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#E5A00D` (Plex Yellow)
- Text + leading play glyph: `#1A1304` (Yellow Ink) — **never white**
- Font: Inter 16pt weight 700; play glyph 16pt, 8pt gap before label
- Padding: 14pt vertical, 28pt horizontal (or full-width on detail screens)
- Pressed: background `#C98A09` + scale 0.98
- Disabled: background `#32373B`, text `#6B7075`

**Secondary / Outline Button ("Mark as Watched")**
- Background: transparent
- Border: 1pt `#3C4146`
- Text: `#F2F3F4`, Inter 14pt weight 600
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `rgba(255,255,255,0.06)`

**Ghost Button ("Add to Playlist")**
- Background: `#32373B`
- Text: `#F2F3F4`, Inter 14pt weight 600
- Corner radius: 8pt
- Padding: 12pt vertical, 20pt horizontal

**Text Button ("More like this")**
- Background: none
- Text: `#E5A00D`, Inter 14pt weight 600
- No underline
- Pressed: opacity 0.6

### Content Card (the core atom)

**Poster Card (library grid)**
- Aspect ratio: 2:3 (vertical poster)
- Corner radius: 6pt
- Image: full-bleed poster art, full color
- Unwatched indicator: a single 9pt yellow (`#E5A00D`) dot, top-right, 7pt inset, soft drop-shadow for legibility on bright art
- Title (optional, below card): Inter 15pt weight 600 `#F2F3F4`, 1-line truncate, 8pt top gap
- Pressed: scale 0.97, 150ms ease-out

**On Deck Tile (signature component)**
- Aspect ratio: 16:9, width ~220pt on iPhone
- Corner radius: 6pt
- Centered play overlay: 42pt circle, `rgba(0,0,0,0.55)` backing, white 16pt play glyph
- Progress bar: pinned to tile bottom, 3pt tall, `rgba(255,255,255,0.22)` track + `#E5A00D` fill at watched fraction
- Title below: Inter 15pt weight 600 `#F2F3F4`, 8pt top gap
- Sub below: Inter 14pt weight 400 `#9BA0A4` — "S2 · E5 · 22 min left" / "2023 · 1h 02m left"

### Server Picker (most distinctive component)

**Server Pill (Home top bar)**
- Background: `#282C30`, 1pt `#34393D` border, fully rounded (999pt)
- Contents: 8pt status dot (green/gray) + server name Inter 13pt weight 600 `#F2F3F4` + 14pt chevron `#9BA0A4`
- Padding: 7pt vertical, 14pt horizontal
- Tap opens the server sheet

**Server Sheet Row**
- Container: 8pt corner radius, `#32373B` (unselected) / `#3C4146` (selected), full width, 12–14pt padding
- Leading: 9pt status dot — `#4CAF50` online / `#6B7075` offline
- Name: Inter 14pt weight 600 `#F2F3F4`
- Meta: Inter 12pt weight 400 `#9BA0A4` — "Online · 2,481 items · LAN" / "Offline · last seen 3d ago"
- Trailing (selected only): 18pt yellow (`#E5A00D`) check mark
- 4pt gap between rows

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area (≈64pt total)
- Background: `rgba(31,35,38,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#34393D`
- Tabs (5): Home, Library, Discover, Live TV, You
- Icon size: 22pt
- Active: `#E5A00D` (filled glyph + label)
- Inactive: `#6B7075` (stroked glyph + label)
- Labels: Inter 10pt weight 600, always shown
- No tint pill — active state is pure yellow color change

**Top Bar (Home)**
- Height: 44pt + safe area
- Background: `#1F2326` (matches canvas)
- Leading: server picker pill
- Trailing: search glyph + settings gear, 20pt, `#F2F3F4`

**Top Bar (detail screen)**
- Compact: back chevron + title (appears on scroll) + cast + overflow
- Hero art sits below; no full-bleed-to-status-bar treatment (Plex keeps a clean top inset)

### Input Fields

**Search Field**
- Height: 44pt
- Background: `#32373B`
- Corner radius: 8pt
- Leading icon: magnifier 16pt `#9BA0A4`
- Placeholder: "Search this server…" Inter 15pt weight 400 `#6B7075`
- Text: `#F2F3F4`
- Focus: 1pt `#E5A00D` border

**Segmented / Filter Chips (library filters)**
- Pill chips, fully rounded, `#32373B` default / `#E5A00D` selected (with `#1A1304` ink)
- Font: Inter 13pt weight 600
- 8pt vertical / 14pt horizontal padding; 8pt gap

### Distinctive Components

**Unwatched Corner Dot**
- A single 9pt `#E5A00D` circle at the top-right of a library poster, 7pt inset, `0 1px 3px rgba(0,0,0,0.5)` shadow for legibility on bright art
- The quiet "you haven't seen this" signal — Plex's most minimal status affordance

**On Deck Progress Bar**
- 3pt tall, `rgba(255,255,255,0.22)` track + `#E5A00D` fill at the exact watched fraction
- Pinned to the bottom of every On Deck tile — the core resume cue

**Server Status Dot**
- 8–9pt circle: `#4CAF50` online / `#6B7075` offline
- Appears in the server pill, every server-sheet row, and beside remote-library headers

**Centered Play Overlay**
- 42pt circle, `rgba(0,0,0,0.55)` backing, white 16pt play triangle, centered on On Deck art
- Tapping the tile (not just the glyph) resumes — the glyph is an affordance cue, not the only hit target

**Quality / Codec Chip**
- Small pill, `#32373B` bg, Inter 12pt weight 600 `#9BA0A4`, 0.2pt tracking — "1080p · 5.1", "4K HDR", "DTS"
- Sits under the title on detail screens; communicates server-side technical truth

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64
- Screen horizontal inset: 20pt
- Card gutter (poster grid): 12pt
- Section vertical gap: 22pt

### Grid & Container
- iPhone: 20pt horizontal insets; library grid is 3-column 2:3, 12pt gutters
- On Deck: horizontally scrolling 16:9 rail, 12pt tile gap, first tile flush to 20pt inset
- iPad: 5–6 column poster grid; detail content max-width 760pt centered
- Server sheet: full-width list, 20pt insets, 4pt row gap

### Whitespace Philosophy
- **Calm density**: even spacing and subtle dividers — the library is organized, not crowded; never loud
- **Charcoal as ground**: the cool `#1F2326` is the negative space — poster art provides the only saturation
- **Single-accent restraint**: yellow appears in ≤5 roles per screen — discipline keeps it meaningful
- **Server-state always legible**: the picker pill and status dots are never hidden — knowing which server is up is core utility

### Border Radius Scale
- Square (0pt): Dividers, full-bleed art edges
- Subtle (4pt): Tech chips, small badges
- Card (6pt): Poster cards, On Deck tiles
- Standard (8pt): Buttons, search field, server rows, sheets-content
- Comfortable (12pt): Bottom sheets, modals
- Pill (500pt): Server pill, filter chips
- Circle (50%): Status dots, play-overlay backing, avatars

## 6. Depth & Elevation

Plex is flat and calm. Depth is communicated by subtle surface steps and dividers, not by heavy shadow stacks.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Library grid, On Deck rail, content on the charcoal canvas |
| Surface Step (Level 1) | Background lift `#1F2326` → `#282C30` → `#32373B` (no shadow) | Server pill, cards, rows |
| Floating Overlay (Level 2) | `0 4px 16px rgba(0,0,0,0.4)` | The centered play-overlay backing, popovers |
| Sheet (Level 3) | `0 -6px 28px rgba(0,0,0,0.5)` | Server picker sheet, action sheets |
| Modal Overlay | `rgba(0,0,0,0.55)` | Dim behind sheets and modals |

**Shadow Philosophy**: Plex avoids shadow-as-style. Elevation is mostly *surface color steps* on the charcoal scale — the server pill is one step up, a selected row is two steps up. Shadows appear only on truly floating things (the play-overlay circle, sheets). The small yellow corner dot is the one element that gets a tiny shadow, purely so it stays legible on bright poster art.

### Motion
- **Server switch**: tapping a server row checks it (yellow check fades in 150ms), the sheet dismisses 250ms ease-out, the Home content cross-fades to the new server's libraries over 250ms
- **On Deck resume**: tapping a tile scales it 0.97 (150ms), then pushes the player; on return the progress bar animates 0 → watched fraction over 350ms ease-out
- **Card press**: scale 0.97 in 150ms ease-out, release back in 150ms
- **Tab switch**: instant content swap; active-tab color cross-fades to yellow over 120ms
- **Sheet present**: bottom sheet slides up 280ms ease-out; backdrop fades to `rgba(0,0,0,0.55)`
- **Status dot update**: a server coming online cross-fades gray → green over 200ms (no bounce — calm)
- **Mark as Watched**: the unwatched corner dot fades out over 200ms; a brief "Marked as watched" toast slides up
- **Pull to refresh**: a subtle yellow spinner; rescans the current server
- **Haptic feedback**: light impact on server selection, tile resume, and Mark-as-Watched; nothing heavier (Plex motion is deliberately quiet)

## 7. Do's and Don'ts

### Do
- Use cool charcoal (`#1F2326`) as the canvas — calm, neutral, NOT true black
- Use Plex Yellow (`#E5A00D`) as the only accent — Play button, active tab, On Deck progress, unwatched dot, selected-server check
- Always pair yellow fills with dark ink (`#1A1304`) — never white-on-yellow
- Show the server picker pill on Home with a live status dot — it's the defining server-driven affordance
- Put a yellow progress bar on every On Deck tile at its watched fraction
- Mark unwatched library posters with a single small yellow corner dot
- Use green (`#4CAF50`) / gray (`#6B7075`) status dots for online / offline servers
- Keep surfaces flat with subtle `#34393D` dividers and surface-color steps for elevation
- Use Inter at functional weights — type organizes, the yellow expresses
- Keep motion quiet and calm (150–280ms ease-out, no bounce)

### Don't
- Don't use true black (`#000000`) as the canvas — Plex is intentionally cool charcoal
- Don't introduce a second accent color — yellow is the only brand color
- Don't put white text/icons on a yellow fill — it fails contrast and breaks the brand; use `#1A1304`
- Don't hide or omit the server picker — multi-server status is core utility, not optional chrome
- Don't use heavy drop-shadows or gradients — Plex is flat; elevation is surface-color steps
- Don't overuse yellow — if it appears in more than ~5 roles per screen it stops meaning anything
- Don't bold body or metadata — meta is secondary gray `#9BA0A4` at weight 400
- Don't add a bouncy or playful motion language — Plex motion is deliberately calm
- Don't ship a pure-white light mode for the library — the charcoal identity must hold in the media experience
- Don't drop the On Deck progress bar — resume position is the primary re-engagement hook
- Don't replace the unwatched corner dot with a loud "NEW" ribbon — the quiet dot is the Plex way

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | On Deck tile ≈ 200pt; 3-col poster grid; tab labels stay |
| iPhone 13/14/15 | 390pt | Standard layout; On Deck tile ≈ 220pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by the clean top inset |
| iPhone 15/16 Pro Max | 430pt | On Deck tile ≈ 250pt; 3-col grid with larger posters |
| iPad (portrait) | 768pt | 5-col poster grid; detail content max-width 760pt centered |
| iPad (landscape) | 1024pt+ | 6-col grid; two-pane library + detail; player fills screen |

### Dynamic Type
- Scales: screen/hero titles, section headers, body, summaries, meta
- Fixed: tab labels, tech chips, progress-bar geometry, status dots, the unwatched corner dot
- Long titles truncate to 1 line on cards, wrap to 2 in the hero

### Orientation
- Portrait-primary for browsing; the video player is landscape-locked full-screen
- iPad supports landscape browsing with a wider grid and optional two-pane detail

### Touch Targets
- Primary Play button: 48pt+ tall
- Tab icons: 22pt glyph, 49pt+ hit area
- On Deck tile: full-tile tap (not just the play glyph), ≥124pt tall
- Server row: full-row tap, ≥44pt tall
- Server pill: ≥36pt tall hit area

### Safe Area Handling
- Top: clean top inset (server pill / nav) respects safe area and the Dynamic Island — no full-bleed-to-status-bar
- Bottom: bottom tab bar + home indicator respected; player controls inset from the home indicator
- Server sheet respects the bottom safe area; its grabber sits inside it
- Sides: 20pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#1F2326` (cool charcoal — NOT true black)
- Surface 1 (pill / sheet): `#282C30`
- Surface 2 (rows / search): `#32373B`
- Surface 3 (selected / track): `#3C4146`
- Divider: `#34393D`
- Text primary: `#F2F3F4`
- Text secondary: `#9BA0A4`
- Text tertiary: `#6B7075`
- Plex Yellow (the only accent): `#E5A00D`
- Yellow pressed: `#C98A09`
- Yellow ink (text on yellow — mandatory): `#1A1304`
- Online green: `#4CAF50`
- Offline gray: `#6B7075`
- Error: `#E5484D`

### Example Component Prompts
- "Build the Plex server picker pill in SwiftUI: a horizontally-hugging capsule, background `#282C30`, 1pt `#34393D` border, fully rounded. Inside, left-to-right: an 8pt status dot (`#4CAF50` if online), the server name in Inter 13pt weight 600 `#F2F3F4`, then a 14pt chevron-down `#9BA0A4`. 7pt vertical / 14pt horizontal padding. Tapping it presents the server sheet."

- "Create a Plex On Deck tile: a 220×124pt 16:9 image, 6pt corner radius. Centered: a 42pt circle with `rgba(0,0,0,0.55)` fill and a white 16pt play triangle. Pinned to the tile bottom: a 3pt progress bar, track `rgba(255,255,255,0.22)`, fill `#E5A00D` at the watched fraction. Below the tile: title in Inter 15pt weight 600 `#F2F3F4`, then a sub line 'S2 · E5 · 22 min left' in Inter 14pt weight 400 `#9BA0A4`."

- "Render the Plex primary Play button: 8pt corner radius, background `#E5A00D`, a 16pt play triangle and the label 'Play' both in `#1A1304` (dark ink — NEVER white), Inter 16pt weight 700, 8pt gap. 14pt vertical / 28pt horizontal padding (full-width on detail screens). Pressed: background `#C98A09` + scale 0.98."

- "Build a Plex server-sheet row: 8pt corner radius, background `#32373B` (or `#3C4146` if it's the selected server), full width, 12–14pt padding. Leading: a 9pt status dot — `#4CAF50` online, `#6B7075` offline. Then a column: name in Inter 14pt weight 600 `#F2F3F4`, meta 'Online · 2,481 items · LAN' in Inter 12pt weight 400 `#9BA0A4`. If selected, a trailing 18pt yellow (`#E5A00D`) check mark. 4pt gap between rows."

- "Create a Plex library poster: a 2:3 image, 6pt corner radius. If the item is unwatched, place a single 9pt `#E5A00D` dot at the top-right with a 7pt inset and a `0 1px 3px rgba(0,0,0,0.5)` shadow so it stays legible on bright art. Optional title below: Inter 15pt weight 600 `#F2F3F4`, 1-line truncate."

- "Create the Plex bottom tab bar: 50pt + safe area, background `rgba(31,35,38,0.96)` with blur(20px) saturate(180%), 0.5pt top border `#34393D`. Five tabs — Home, Library, Discover, Live TV, You — 22pt glyphs with Inter 10pt weight 600 labels always shown. Active: filled glyph + label in `#E5A00D`. Inactive: stroked glyph + label in `#6B7075`. No tint pill."

### Iteration Guide
1. Canvas is cool charcoal `#1F2326` — calm and neutral, NEVER true black
2. Plex Yellow `#E5A00D` is the only accent — Play, active tab, On Deck progress, unwatched dot, selected-server check
3. Yellow fills ALWAYS carry dark ink `#1A1304` — never white-on-yellow (contrast + brand)
4. The server picker pill (status dot + name + chevron) is the defining server-driven affordance — always visible on Home
5. Every On Deck tile has a centered play overlay + a yellow progress bar at its watched fraction
6. Unwatched library posters carry exactly one small yellow corner dot — the quiet status signal
7. Server status uses green `#4CAF50` (online) / gray `#6B7075` (offline) dots; the selected server gets a yellow check
8. Surfaces are flat — elevation is surface-color steps on the charcoal scale, not shadows or gradients
9. Inter at functional weights — type organizes the library; the yellow is the only expressive element
10. Motion is quiet and calm (150–280ms ease-out, no bounce) — Plex is utility, not spectacle
