# Design System Inspiration of Pandora (iOS)

## 1. Visual Theme & Atmosphere

Pandora's iOS app is built around the **thumb** — the up/down gesture that has been its defining interaction since 2000. Pandora pioneered the Music Genome Project: you start a station from a song or artist, and every track that plays is an experiment. A **thumb up** tells the genome "more like this" and shapes the station forever; a **thumb down** skips the track and tunes the station away from it. Unlike a like button bolted onto a catalog app, the thumb *is* the product — it's how a Pandora station learns and becomes yours. The entire app is engineered around two surfaces: the **Now Playing** screen (where the thumb lives, framed by album art) and the **station list** (your collection of genome-built radio stations, which is the app's home).

The brand is anchored by **Pandora Blue** (`#224099`), a deep, classic blue that the app darkens into a near-navy canvas (`#0B0F1C`) for its dark-first identity. Layered on top is **Bright Blue** (`#3668FF`) — the constant interactive accent: the active thumb-up fill, the big circular play button, the scrubber fill, and every primary CTA. The signature visual move on Now Playing is the **album-art gradient hero**: a color pulled from the current track's artwork bleeds down from the top of the screen into the navy floor, and the chrome (thumbs, transport, scrubber) floats over it with *no card frame* — the artwork and its color own the screen. The station list, by contrast, is calm and structured: square station art tiles, station name, and a thin equalizer animation on whatever's currently playing.

The thumb interaction has a deliberate **asymmetry** that the visual system reinforces. The thumb-up, when active, fills solid Bright Blue — a confident, committed state, because a thumb-up permanently shapes the station. The thumb-down stays a hollow outline even when "active," because its job is to skip-and-move-on, not to commit. This asymmetry — solid up, hollow down — is a core signature: it tells the user, at a glance, that a thumb-up is a lasting vote and a thumb-down is a one-time correction. Recently-thumbed tracks animate the icon with a quick scale pop and a haptic.

Typography is **Pandora's brand sans** (a clean grotesque) with **Inter** as the open-source fallback for implementation. Hierarchy is music-app standard but confident: track titles are heavy (800) on Now Playing so they read over busy artwork; station names are 600; metadata and timecodes are calm and tabular. The overall feel is **focused and a little nostalgic** — Pandora is the original lean-back radio experience, not an interactive catalog, so the UI keeps the controls few, large, and obvious: thumb down, skip back, big play, skip forward, thumb up.

**Key Characteristics:**
- **The thumb up/down IS the product** — the Music Genome Project gesture that shapes every station
- **Thumb asymmetry** — active thumb-up fills solid Bright Blue (a lasting vote); thumb-down stays a hollow outline (a one-time skip)
- **Now Playing = album-art gradient hero** — a color from the artwork bleeds top-down into the navy floor; chrome floats with NO card frame
- **The station list is home** — genome-built radio stations as square-art rows with a now-playing equalizer
- **Pandora Blue `#224099`** deepens to a near-navy canvas (`#0B0F1C`) — dark-first, never neutral gray
- **Bright Blue `#3668FF`** is the constant accent — active thumb-up, play button, scrubber fill, every primary CTA
- **Lean-back transport** — five large controls: thumb down · skip back · big play · skip forward · thumb up
- **Album artwork is sacred** — never tinted; only the surrounding gradient/UI shifts per track
- **Calm, tabular metadata** — timecodes and counts are tabular; track titles heavy enough to read over art
- **Bottom tab bar** — For You / Browse / My Collection / Recents — text-primary active, no pill

## 2. Color Palette & Roles

### Primary (Interactive)
- **Bright Blue** (`#3668FF`): THE interactive accent — active thumb-up fill, the circular play button, scrubber fill, primary CTA, active selection. The "do this / this is on" color.
- **Bright Blue Pressed** (`#2A55D8`): Pressed state of the play button and primary CTA.
- **Pandora Blue** (`#224099`): The brand-heritage blue — used in marketing, the splash, deep gradient bases, and as the hue the dark canvas is derived from.

### Canvas & Surfaces (Dark Mode — primary)
- **Navy Canvas** (`#0B0F1C`): Primary dark canvas — a deep blue-navy derived from Pandora Blue, NOT neutral black/gray.
- **Surface 1** (`#141A2B`): Card and sheet fills, station-list backdrop sections.
- **Surface 2** (`#1E2538`): Raised rows (the active station row), the scrubber track, segmented controls.
- **Divider** (`#28304A`): Hairline dividers between rows and sections.
- **Now Playing Gradient**: top color derived from album art → `#1A2240` mid → `#0B0F1C` floor.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#FFFFFF`): Primary light canvas (used in some settings/account flows).
- **Surface 1** (`#F4F6FB`): Grouped backdrop, list sections.
- **Surface 2** (`#FFFFFF`): Card fills lifted above Surface 1.
- **Divider** (`#E4E8F2`): Hairline dividers.

### Text
- **Text Primary Dark** (`#EDF0F7`): Primary text on the navy canvas.
- **Text Secondary Dark** (`#9AA3BD`): Artist names, metadata, the idle thumb-down outline.
- **Text Tertiary Dark** (`#646E8C`): Timecodes, captions, disabled.
- **Text Primary Light** (`#11162A`): Primary text on light.
- **Text Secondary Light** (`#5C6680`): Metadata on light.
- **Text On Bright** (`#FFFFFF`): Text/icon on the Bright Blue play button and CTA.

### Thumb States (signature)

| State | Treatment |
|-------|-----------|
| Thumb Up — active | Solid fill `#3668FF`, stroke `#3668FF` — a committed, lasting vote |
| Thumb Up — idle | Hollow, stroke `#646E8C` (tertiary) |
| Thumb Down — active | Hollow still, stroke `#9AA3BD` (secondary, slightly brighter than idle) — a one-time skip, never solid |
| Thumb Down — idle | Hollow, stroke `#646E8C` (tertiary) |

### Semantic

| Role | Dark | Light |
|------|------|-------|
| Success / Saved | `#2BC48A` | `#1E9E6E` |
| Warning | `#F2A93A` | `#D9871C` |
| Error | `#F0526B` | `#D63A53` |
| Info / Active | `#3668FF` | `#224099` |
| Premium / Upgrade | `#3668FF` | `#224099` |
| Now-playing equalizer | `#3668FF` | `#3668FF` |

### Semantic Notes
- **Bright Blue is the through-line**: the active thumb-up, the play button, the scrubber fill, and the primary CTA are all `#3668FF` — one consistent "this is the action / this is on" color.
- **The thumb-up is the only solid-filled control**: nothing else fills solid bright blue except the play button — the genome's "yes" is visually privileged.
- **Thumb-down never goes solid**: even active, it's an outline — this asymmetry is intentional and a hard rule.
- **Album art is sacred**: never tinted, never color-blocked — the Now Playing gradient samples *from* it but never paints *over* it.

## 3. Typography Rules

### Font Family
- **Brand Font**: Pandora's brand sans (a clean modern grotesque used across product and marketing).
- **Implementation Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — closest free grotesque-humanist sans with strong numerals and a wide weight range; use for clones.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: tabular lining figures for timecodes, thumb counts, and durations so they don't jitter.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Inter | 32pt | 800 | 1.15 | -0.4pt | "My Collection" tab header |
| Screen Title | Inter | 26pt | 800 | 1.2 | -0.3pt | "Browse stations" |
| Track Title | Inter | 21pt | 800 | 1.2 | -0.3pt | Now Playing track — heavy to read over art |
| Section Header | Inter | 17pt | 700 | 1.3 | -0.1pt | "Recommended for you" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, station info |
| Station Name | Inter | 15pt | 600 | 1.35 | 0pt | Station-list row title |
| Artist / Meta | Inter | 14pt | 400 | 1.4 | 0pt | "The Glass Hours", row subtitle |
| Album Line | Inter | 13pt | 500 | 1.4 | 0pt | "Neon Reverie · 2023" under the title |
| Eyebrow | Inter | 12pt | 700 | 1.2 | 1.0pt | "STATION" above the station name — uppercase, tracked |
| Time / Caption | Inter | 11pt | 500 | 1.3 | 0pt | "1:42 · -2:18" — tabular |
| Button | Inter | 16pt | 700 | 1.0 | 0.1pt | Primary/secondary button labels |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Thumb Count | Inter | 12pt | 600 | 1.0 | 0pt | "312 thumbs up" — tabular |

### Principles
- **Track title carries the screen**: on Now Playing the title is 21pt/800 so it stays legible over busy, colorful album art and the gradient
- **Calm metadata**: artist, album, station info are 400–600 weight — they support the music, never shout
- **Tabular time & counts**: timecodes, durations, and thumb counts use tabular lining numerals so they never jitter as they tick
- **Tracked uppercase eyebrows**: the "STATION" label above the station name is 12pt 700 uppercase +1.0pt tracking — a quiet signpost
- **Dynamic Type**: display, screen title, track title, body, station name scale; tab labels, eyebrow, timecodes stay fixed (layout-critical)

## 4. Component Stylings

### Buttons

**Primary Button (Create station / Done)**
- Shape: pill, 500pt corner radius (Pandora's CTA shape)
- Background: `#3668FF` (Bright Blue)
- Text: `#FFFFFF` Inter 16pt weight 700
- Height: 50pt
- Pressed: background `#2A55D8` + scale 0.98
- Disabled: `#3668FF` at 40% opacity

**Outline Button (Shuffle)**
- Background: transparent
- Border: 1.5pt `#9AA3BD` (text-secondary)
- Text: `#EDF0F7` Inter 14pt weight 600
- Height: 44pt, pill radius
- Pressed: 8% white fill

**Text Button (Upgrade to Premium)**
- Inter 14pt weight 700, color `#3668FF`
- No underline; pressed: 60% opacity

### Thumb Up / Down (the signature atom)
- Two icons flanking the transport row on Now Playing, 44pt hit each, 26pt glyph
- **Thumb Up — active**: solid fill `#3668FF`, stroke `#3668FF` — the only filled control besides Play; a lasting genome vote
- **Thumb Up — idle**: hollow, stroke `#646E8C`
- **Thumb Down — active**: hollow, stroke `#9AA3BD` (brighter than idle but NEVER solid) — a one-time skip-and-tune
- **Thumb Down — idle**: hollow, stroke `#646E8C`
- Tap feedback: icon scales `1.0 → 1.2 → 1.0` over 240ms + light haptic; a thumb-down also triggers a track skip
- The asymmetry (solid up / hollow down) is a hard rule — it encodes that up commits, down corrects

### Now Playing (the signature surface)
- Full-screen, NO card frame
- **Album-art gradient hero**: a dominant color sampled from the current artwork → `#1A2240` mid → `#0B0F1C` floor, top-down vertical gradient behind everything
- Top bar: down-chevron (collapse), centered "STATION / {station name}" eyebrow+name, ⋯ overflow — floating over the gradient
- Album art: square, 10pt corner radius, centered, ~76% screen width, deep soft shadow (`rgba(0,0,0,0.7) 0 24px 50px -16px`)
- Track block: title 21pt/800, artist 15pt/500 secondary, album line 13pt/500 tertiary
- Scrubber: 4pt track `Surface 2`, fill `#3668FF`, 12pt white knob; elapsed / remaining timecodes tabular tertiary
- Transport row: thumb-down · skip-back · 66pt circular Bright Blue play · skip-forward · thumb-up

### Station List Row (the home atom)
- Row, 12pt corner radius on the active row, full-width
- Leading 52pt square station art, 8pt corner radius
- Station name 15pt/700; subtitle 12pt secondary ("Now playing · {artist}" or "Station · 312 thumbs up")
- Trailing: a 4-bar **equalizer** animation (`#3668FF`) on the currently-playing station; otherwise empty or a ⋯
- Active row gets a `Surface 2` background fill

### Mini Player
- Pinned above the tab bar on non-Now-Playing screens
- 56pt tall: 40pt square art + title/artist (one line each) + a small play/pause + thumb-up
- Tapping it expands into the full Now Playing (shared-element art transition)
- Background: blurred navy `rgba(11,15,28,0.95)`, 0.5pt top divider

### Navigation

**Bottom Tab Bar**
- Height: 66pt total (50pt + safe area)
- Background: `#0B0F1C` (blurred on scroll), 0.5pt top divider `#28304A`
- Tabs (4): For You, Browse, My Collection, Recents
- Icon 22pt; active fill, inactive stroke
- Active color: `#EDF0F7` (text-primary) — icon + label; NO pill/indicator
- Inactive: `#646E8C`
- Labels: Inter 10pt weight 600, always shown

**Top Bar**
- On Now Playing: transparent over the gradient (collapse chevron + station + overflow)
- Elsewhere: solid navy, screen title 26pt/800, optional back chevron

### Input Fields

**Search Field (Search music & podcasts)**
- Height: 44pt, pill radius (500pt)
- Background: `Surface 2`
- Leading 18pt search icon secondary; placeholder 15pt 400 secondary
- Focus: 1.5pt `#3668FF` border

### Distinctive Components

**Thumb Up / Down Pair**
- The Music Genome gesture — see the signature atom above. The solid-up / hollow-down asymmetry is Pandora's most recognizable UI signature.

**Album-Art Gradient Hero**
- The Now Playing background: a color extracted from the artwork bleeding into the navy floor; re-derived on every track change with a 400ms cross-fade.

**Now-Playing Equalizer Bars**
- A tiny 4-bar animated equalizer in `#3668FF` marking the active station in the list and the mini player.

**Create Station Sheet**
- Bottom sheet: search a song/artist/genre → "Create station" pill CTA → the new genome station opens straight into Now Playing.

**Station Detail / Tune-In**
- A station's page: large art, thumb history ("312 thumbs up · 28 thumbs down"), "Add variety", "Edit station" — the genome controls surfaced.

**My Collection**
- Grid/list of saved stations, playlists, and podcasts — the lean-back home; tap a station tile to instantly start radio.

**Crowd-Faces Why-This-Track**
- The "Why did you play this song?" panel explaining the genome traits the track shares with the station seed — Pandora's transparency hallmark.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt; Now Playing art uses ~32pt side inset (centered, large)
- Station-row vertical padding: 10pt; row gap: 4pt
- Now Playing is vertically rhythmic: art → track → scrubber → transport, generous gaps so controls are thumb-reachable

### Grid & Container
- iPhone: single column; Now Playing full-screen with the gradient hero; mini player + tab bar pinned
- iPad / large: station list in a multi-column grid; Now Playing centered with art capped, gradient still full-bleed
- The transport controls always sit in the lower third for one-handed reach

### Whitespace Philosophy
- **Let the art lead**: Now Playing has no card frames — the artwork and its gradient own the screen; controls float
- **Calm lists**: the station list is structured and quiet so the music (and the equalizer) is the only motion
- **Few, large controls**: lean-back radio means a small set of big targets — thumb, skip, play — never a dense control panel
- **The thumb has room**: thumbs sit clearly apart from skip/play so a genome vote is never a mis-tap

### Border Radius Scale
- Square (0pt): the Now Playing gradient (full-bleed), dividers
- Subtle (4pt): scrubber track, tiny chips
- Standard (8pt): station-art tiles, mini-player art
- Art (10pt): the Now Playing album art
- Card (12pt): active station row, sheets' inner cards
- Comfortable (16pt): bottom sheets, modals
- Pill (500pt): buttons, search field, filter chips
- Circle (50%): the play button, avatars, the thumb hit areas

## 6. Depth & Elevation

Pandora uses minimal elevation — the album-art gradient provides the depth; UI is mostly flat over it.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Navy canvas, station rows, the gradient itself |
| Art (Level 1) | `rgba(0,0,0,0.7) 0 24px 50px -16px` | The Now Playing album art (deep soft lift off the gradient) |
| Play Button (Level 1) | `rgba(54,104,255,0.7) 0 10px 26px -8px` (blue glow) | The circular Bright Blue play button |
| Pinned (Level 2) | Top divider + backdrop blur (`rgba(11,15,28,0.95)`) | Mini player + tab bar |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -8px 28px` | Create-station, station-detail, queue sheets |
| Modal Overlay | `rgba(5,7,14,0.6)` scrim | Behind modals and sheets |

**Shadow Philosophy**: the album art floats on a deep soft shadow so it reads as the hero object over the gradient; the play button gets a *blue glow* echoing Bright Blue so it's unmistakably the primary control. Everything else is flat over the navy. On the rare light surfaces, cards switch to a faint cool shadow.

### Motion
- **Thumb tap**: icon scales `1.0 → 1.2 → 1.0` over 240ms ease-out; thumb-up cross-fades hollow→solid Bright Blue; light haptic. Thumb-down additionally triggers the skip transition
- **Track change**: album art cross-fades + slight scale `0.96 → 1.0` over 320ms; the gradient re-derives from the new art and cross-fades over 400ms; title/artist swap with a 200ms fade
- **Skip forward/back**: art slides horizontally 1:1-ish then settles 260ms ease-out
- **Play/pause**: icon morphs play↔pause 150ms; button scale `1.0 → 0.96 → 1.0` on press
- **Scrubber drag**: knob tracks the finger 1:1; fill follows; timecodes update live (tabular, no layout shift)
- **Mini → Now Playing**: shared-element art expands into the full hero over 320ms ease-out; gradient fades in
- **Station start**: tapping a station row pulses its equalizer in and pushes Now Playing (300ms)
- **Tab change**: instant icon/label tint to text-primary; no pill slide (there is no pill)
- **Sheet present**: bottom sheet rises 320ms ease-out + scrim fade
- **Haptics**: light on thumb up/down and play; soft on skip; success on station created

## 7. Do's and Don'ts

### Do
- Make the thumb up/down the centerpiece of Now Playing — it is the Music Genome product
- Keep the thumb asymmetry: active thumb-up fills solid Bright Blue; thumb-down stays a hollow outline always
- Build Now Playing as an album-art gradient hero with NO card frame — chrome floats over it
- Use `#3668FF` consistently for the active thumb-up, play button, scrubber fill, and primary CTA
- Use a deep blue-navy canvas (`#0B0F1C`) derived from Pandora Blue — dark-first
- Make the station list the home — genome-built stations as square-art rows with a now-playing equalizer
- Keep transport lean: thumb-down · skip-back · big play · skip-forward · thumb-up — few, large targets
- Re-derive the Now Playing gradient from each track's artwork with a smooth cross-fade
- Use tabular numerals for timecodes, durations, and thumb counts so they never jitter
- Use the pill shape for buttons and the search field; circular for the play button and thumb hit areas
- Keep metadata calm (400–600) and the track title heavy (800) so it reads over art

### Don't
- Don't fill the thumb-down solid — even active it stays a hollow outline (the asymmetry is a hard rule)
- Don't bolt the thumb on as a generic "like" — it shapes the station; treat it as the core mechanic
- Don't put a card frame around Now Playing — the artwork and its gradient own the screen
- Don't tint or color-block over album artwork — the gradient samples from it, never paints over it
- Don't use a neutral gray/black canvas — it must be the blue-navy `#0B0F1C` from Pandora Blue
- Don't add a tab-bar pill/indicator — active is a text-primary tint only
- Don't crowd the transport — lean-back radio means few, large, well-spaced controls
- Don't let timecodes/counts jitter — always tabular lining numerals
- Don't make the play button anything but the circular Bright Blue (with its blue glow) primary control
- Don't animate aggressively — track changes and gradient shifts are smooth 200–400ms cross-fades
- Don't bury the station list — it is the app's home, not a secondary tab afterthought

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Album art ~70% width; transport tighter but thumbs stay ≥44pt apart |
| iPhone 13/14/15 | 390pt | Standard layout; album art ~76% width |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; top bar clears the island |
| iPhone 15/16 Pro Max | 430pt | Larger album art; more vertical breathing room in the transport |
| iPad (portrait) | 768pt | Station list multi-column grid; Now Playing centered, art capped ~520pt, gradient full-bleed |
| iPad (landscape) | 1024pt+ | Split: station list / collection on the left, Now Playing detail on the right |

### Dynamic Type
- Scales: display, screen title, track title, body, station name
- Fixed (layout-critical): tab labels, eyebrow, timecodes, thumb counts
- Album art and the gradient never scale (art-driven)

### Orientation
- iPhone: portrait-primary; Now Playing supports landscape (art + transport reflow side-by-side)
- iPad: full rotation; the split layout reflows

### Touch Targets
- Thumb up / down: 44pt hit each, clearly separated from skip/play
- Play button: 66pt
- Skip back/forward: 40pt glyph, 44pt+ hit
- Station row: full-row tappable, ≥ 64pt
- Tab icons: 22pt glyph, 48pt+ hit

### Safe Area Handling
- Top: Now Playing top bar respects the Dynamic Island/notch over the gradient
- Bottom: mini player + tab bar sit above the home indicator; the gradient extends under both
- Now Playing transport stays clear of the home indicator for one-handed reach

## 9. Agent Prompt Guide

### Quick Color Reference
- Interactive accent / active thumb-up / play / scrubber (Bright Blue): `#3668FF` (pressed `#2A55D8`)
- Brand-heritage blue (gradient base, marketing): `#224099`
- Canvas (dark): `#0B0F1C` · surface 1 `#141A2B` · surface 2 `#1E2538`
- Canvas (light): `#FFFFFF` · surface 1 `#F4F6FB`
- Divider: `#28304A` dark / `#E4E8F2` light
- Text primary: `#EDF0F7` dark / `#11162A` light
- Text secondary: `#9AA3BD` dark · tertiary `#646E8C` dark
- Thumb-up active fill: `#3668FF` · thumb-down active stroke: `#9AA3BD` (never filled)
- Success: `#2BC48A` dark · equalizer: `#3668FF`

### Example Component Prompts
- "Build Pandora's thumb up/down pair in SwiftUI: two 44pt-hit icons flanking the transport row, 26pt glyph. Thumb-up — active: solid fill `#3668FF`, stroke `#3668FF`; idle: hollow, stroke `#646E8C`. Thumb-down — active: hollow, stroke `#9AA3BD` (NEVER solid); idle: hollow, stroke `#646E8C`. On tap, scale `1.0 → 1.2 → 1.0` over 240ms + light haptic; thumb-down also triggers a track skip. The solid-up / hollow-down asymmetry is mandatory — it encodes that up commits and down corrects."

- "Build Pandora's Now Playing screen: full-screen, NO card frame. Background is a top-down gradient — a color sampled from the album art → `#1A2240` → `#0B0F1C` floor. Top bar floating over it: collapse chevron, centered 'STATION' eyebrow (12pt w700 uppercase +1pt) + station name (13pt w700), ⋯ overflow. Centered square album art, 10pt radius, ~76% width, shadow `rgba(0,0,0,0.7) 0 24px 50px -16px`. Track title 21pt w800, artist 15pt w500 `#9AA3BD`, album 13pt w500 `#646E8C`. Scrubber: 4pt `#1E2538` track, `#3668FF` fill, 12pt white knob, tabular timecodes. Transport: thumb-down · skip-back · 66pt circular `#3668FF` play (blue-glow shadow) · skip-forward · thumb-up."

- "Create a Pandora station-list row: full-width, 52pt square station art (8pt radius) leading, station name 15pt w700 `#EDF0F7`, subtitle 12pt `#9AA3BD` ('Now playing · The Glass Hours'). On the currently-playing station: trailing 4-bar animated equalizer in `#3668FF` and a `#1E2538` row background (12pt radius). Tap → push Now Playing."

- "Build the Pandora play button: 66pt circle, `#3668FF` fill, white play glyph, shadow `rgba(54,104,255,0.7) 0 10px 26px -8px` (blue glow). Pressed: `#2A55D8` + scale 0.96. It is the only solid-blue control besides an active thumb-up."

- "Build the Pandora bottom tab bar: 4 tabs For You / Browse / My Collection / Recents, height 66pt, `#0B0F1C` bg, 0.5pt `#28304A` top divider. Active tint `#EDF0F7` (icon + label), inactive `#646E8C`. NO pill/indicator — color tint only. Labels Inter 10pt w600 always visible."

- "Build the Pandora mini player: pinned above the tab bar, 56pt tall, blurred `rgba(11,15,28,0.95)` with a 0.5pt `#28304A` top divider. 40pt square art + one-line title/artist + small play/pause + thumb-up. Tap expands into full Now Playing with a shared-element art transition."

### Iteration Guide
1. The thumb up/down IS the product — the Music Genome gesture that shapes every station; never a generic like
2. Thumb asymmetry is a hard rule: active thumb-up fills solid `#3668FF`; thumb-down stays a hollow outline always
3. Now Playing is an album-art gradient hero with NO card frame — a color from the art bleeds into the navy floor; chrome floats
4. `#3668FF` is the constant — active thumb-up, the circular play button, the scrubber fill, every primary CTA
5. The canvas is a deep blue-navy `#0B0F1C` derived from Pandora Blue `#224099` — dark-first, never neutral gray
6. The station list is the home — genome stations as square-art rows; the currently-playing one has a `#3668FF` equalizer
7. Transport is lean-back: thumb-down · skip-back · big play · skip-forward · thumb-up — few, large, well-spaced
8. Re-derive the Now Playing gradient from each track's artwork with a smooth 400ms cross-fade; never tint the art
9. Timecodes, durations, and thumb counts are tabular so they never jitter; track title is heavy (800) to read over art
10. Buttons and the search field are pills; the play button and thumb hit areas are circular; motion is smooth 200–400ms
