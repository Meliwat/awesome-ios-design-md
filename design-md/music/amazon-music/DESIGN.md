# Design System Inspiration of Amazon Music (iOS)

## 1. Visual Theme & Atmosphere

Amazon Music's iOS app is built on a **deep teal-navy canvas (`#0C1B22`)** — not a neutral grey or pure black, but a saturated dark blue-green that gives the app an instantly recognizable oceanic identity, distinct from Spotify's neutral `#121212` or YouTube Music's near-black `#030303`. This single decision — using a *tinted* dark instead of a neutral one — is the visual thumbprint of the brand. The app's accent, **Amazon Music Cyan (`#00A8E1`)**, is drawn straight from the canvas's hue family, so the whole product feels like one continuous gradient of blue: the dark canvas, the cyan controls, and the brighter glow cyan (`#25D1DA`) all sit on the same axis.

The signature surface is the **full-screen player with the X-Ray lyrics panel**. X-Ray is Amazon's cross-product feature brand (it powers actor/scene info in Prime Video and trivia in Audible); in Amazon Music it surfaces **synced, line-by-line lyrics plus artist facts and song credits**. On the player screen, the X-Ray lyrics appear in a tinted panel directly below the track metadata: the **currently-sung line is highlighted in bright cyan (`#25D1DA`)**, the next line in secondary text, and surrounding lines dimmed to tertiary. Tapping any line seeks to that moment. This synced-lyric panel, badged with the cyan "X-RAY" chip, is the moment that no competitor replicates the same way — it is the brand's signature.

The player itself sits on a **top-down gradient** that lifts from the canvas toward a lighter teal (`#16404C`) behind the album art, then settles back into `#0C1B22` toward the controls. Album art is a 196pt rounded square (6pt radius — tighter than Spotify's 8pt). The primary play control is a **cyan circle with a white glyph** and a soft cyan glow shadow — cyan *is* the action color here (unlike YouTube Music, where the play button is white). Rows of album art on Home (Recently played, Your Soundtrack, recommended stations) are horizontal-scroll shelves.

Typography is **Amazon Ember** — Amazon's corporate typeface, used across Amazon.com, Kindle, Alexa, and all Amazon apps. It is a clean humanist sans with slightly open apertures. On iOS without the bundled face it falls back to Inter, then SF Pro. Hierarchy is product-UI: an 800-weight screen greeting ("Good evening"), 700 for the player title and section heads, 500 for list-row titles, 400 for body. The X-Ray lyric line is its own 15pt 600 role because the lyric panel is the signature component.

**Key Characteristics:**
- Deep teal-navy canvas (`#0C1B22`) — a tinted dark, not neutral grey; the brand thumbprint
- Full-screen player with X-Ray lyrics — synced line-by-line, current line in bright cyan
- "X-RAY" badge — the cyan chip marking the lyrics/credits panel (Amazon's cross-product feature brand)
- Amazon Cyan (`#00A8E1`) accent + glow cyan (`#25D1DA`) — both drawn from the canvas hue
- Cyan play button — a cyan circle with a white glyph and a soft cyan glow (cyan IS the action)
- Top-down player gradient — lifts toward `#16404C` behind the art, settles to `#0C1B22`
- Amazon Ember type — Amazon's corporate face, shared across Amazon.com / Kindle / Alexa
- Rows of art on Home — Recently played / Your Soundtrack / stations as horizontal shelves
- Bright-cyan active tab — `#25D1DA`, brighter than the structural `#00A8E1`
- 4-tab bar: Home / Find / Podcasts / Library

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Amazon Cyan** (`#00A8E1`): Structural accent — play button fill, scrubber fill & thumb, primary CTA pills.
- **Cyan Bright / Glow** (`#25D1DA`): Active X-Ray lyric line, active tab, focus glow, "X-RAY" badge fill.
- **Cyan Pressed** (`#0086B3`): Pressed state of cyan CTAs and the play button.

### Canvas & Surfaces (Dark — the primary mode)
- **Canvas** (`#0C1B22`): Primary app background — deep teal-navy, the brand thumbprint.
- **Surface 1** (`#122A33`): Cards, sheets, raised rows.
- **Surface 2** (`#1A3742`): Pressed/hover rows, the mini-player bar, secondary chips.
- **Divider** (`#234653`): Hairline separators (a desaturated teal, not grey).
- **Gradient Top** (`#16404C`): The lighter teal the player gradient lifts toward behind the art.
- **X-Ray Panel Fill** (`rgba(0,168,225,0.07)`): The tinted background of the X-Ray lyrics panel.
- **X-Ray Panel Border** (`rgba(37,209,218,0.22)`): The faint cyan stroke around the X-Ray panel.

### Text
- **Text Primary** (`#FFFFFF`): Track names, screen titles, primary labels.
- **Text Secondary** (`#9FB6BF`): Artist names, subtitles, next-lyric line, source eyebrow (a teal-tinted grey, not neutral).
- **Text Tertiary** (`#6B8693`): Dimmed surrounding lyric lines, disabled, low-emphasis metadata.
- **On-Cyan Text** (`#FFFFFF`): Glyph inside the cyan play button, text on cyan CTAs.
- **On-Bright Text** (`#042730`): Text on the bright-cyan "X-RAY" badge (dark-on-bright for contrast).

### Semantic
- **Success** (`#2EC4B6`): Download-complete, added-to-library confirmation (a teal-green, on-brand).
- **Error** (`#FF6B6B`): Playback error, failed download toast.
- **HD / Ultra HD badge** (`#25D1DA` on `#0C1B22`): Quality badges use the glow cyan.
- **Dolby Atmos accent** (`#9FB6BF`): "Dolby Atmos" / "360 Reality Audio" capability labels.

## 3. Typography Rules

### Font Family
- **Primary**: `Amazon Ember` (Amazon's proprietary corporate typeface, by Dalton Maag for Amazon) — humanist sans with open apertures, used across Amazon.com, Kindle, Alexa, Prime Video, and all Amazon apps.
- **Numerals**: Amazon Ember tabular figures for timestamps and durations.
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Helvetica, Arial, sans-serif`
- **Why Inter as the documented fallback**: Inter's humanist proportions and x-height closely track Amazon Ember, so layouts built against Inter transfer faithfully when Ember is bundled.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Greeting | Ember | 32pt | 800 | 1.15 | -0.4pt | "Good evening", "Good morning" — time-of-day hero |
| Player Title | Ember | 26pt | 700 | 1.2 | -0.3pt | Track name on full-screen player |
| Section Header | Ember | 22pt | 700 | 1.25 | -0.2pt | "Recently played", "Made for you" |
| Card / Shelf Title | Ember | 18pt | 700 | 1.3 | -0.1pt | Playlist / station card titles |
| Body | Ember | 16pt | 400 | 1.5 | 0pt | Descriptions, about copy |
| Row Title | Ember | 15pt | 500 | 1.35 | 0pt | Song-row primary line |
| X-Ray Lyric Line | Ember | 15pt | 600 | 1.55 | 0pt | Synced lyric line — current in bright cyan |
| Subtitle / Meta | Ember | 14pt | 400 | 1.4 | 0pt | Artist · album, "PLAYING FROM PLAYLIST" |
| X-Ray Label | Ember | 10pt | 700 | 1.0 | 0.6pt | "LYRICS" / "ABOUT" eyebrow, uppercase |
| X-Ray Badge | Ember | 9pt | 800 | 1.0 | 0.5pt | The "X-RAY" chip text |
| Chip Label | Ember | 13pt | 600 | 1.0 | 0pt | Genre / mood / quality chips |
| Timestamp | Ember | 11pt | 400 | 1.0 | 0pt | Tabular figures, scrubber time |
| Tab Label | Ember | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Ember | 15pt | 700 | 1.0 | 0pt | Play / Shuffle / Add to library |

### Principles
- **Product-UI rhythm**: Heavy 800 greeting, 700 section heads — app-like, not editorial.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Body 400, rows 500, lyric line 600, headers/buttons 700, greeting 800.
- **Ember is the brand**: Never swap for a serif; Inter is the only acceptable substitute and only because it tracks Ember's metrics.
- **Tabular numerals for time**: Scrubber and durations use tabular figures so digits don't jitter.
- **The lyric line is its own role**: 15pt 600 — the X-Ray panel is the signature, so its type is deliberate.
- **Dynamic Type**: Greeting, section header, body, row title, lyric line scale; X-Ray badge, tab labels, timestamps stay fixed.

## 4. Component Stylings

### Buttons

**Primary Play Button (Player)**
- Shape: Circle, 60pt diameter (52pt in compact contexts)
- Background: `#00A8E1` (Amazon Cyan) — cyan IS the action color
- Glyph: play/pause in `#FFFFFF`, 24pt
- Shadow: `0 8pt 22pt -6pt rgba(0,168,225,0.55)` — a soft cyan glow
- Pressed: background `#0086B3` + scale 0.97

**Primary CTA Button (Play / Shuffle Play)**
- Shape: Pill, 999pt corner radius
- Background: `#00A8E1`
- Text: `#FFFFFF`, Ember 15pt weight 700
- Padding: 13pt vertical, 28pt horizontal
- Pressed: background `#0086B3` + scale 0.98

**Outline Button (Shuffle / Radio)**
- Background: transparent
- Text: `#25D1DA` (glow cyan), Ember 14pt weight 600
- Border: 1pt `#25D1DA`
- Padding: 11pt vertical, 22pt horizontal
- Corner radius: 999pt
- Pressed: background `rgba(0,168,225,0.1)`

**Ghost Button (Add to library / Follow)**
- Background: `#1A3742` (Surface 2)
- Text: `#FFFFFF`, Ember 14pt weight 600
- Border: 1pt `#234653`
- Padding: 11pt vertical, 20pt horizontal
- Corner radius: 999pt
- Pressed: `#234653`

**Text Button ("See all", "More")**
- Font: Ember 14pt weight 700
- Color: `#25D1DA`
- No underline, no background
- Pressed: opacity 0.6

**Icon Button (skip / repeat / shuffle / ⋮)**
- Glyph: 22–24pt in a 44pt hit area
- Primary controls (`backward`/`forward`): `#FFFFFF`
- Secondary controls (repeat/shuffle at rest): `#9FB6BF`; active: `#25D1DA`
- Pressed: opacity 0.6

### X-Ray Lyrics Panel (Signature)
- Container: `rgba(0,168,225,0.07)` fill, `rgba(37,209,218,0.22)` 1pt border, 10pt corner radius
- Padding: 14pt vertical, 16pt horizontal; positioned directly below player metadata, 16pt below it
- Header row: `"X-RAY"` badge + `"LYRICS"` label
  - Badge: `#25D1DA` background, `#042730` text, Ember 9pt 800, 0.5pt tracking, 4pt radius
  - Label: `#9FB6BF`, Ember 10pt 700, 0.6pt tracking, uppercase
- Lyric lines: Ember 15pt 600, 1.55 line-height
  - Current (sung) line: `#25D1DA` (bright cyan)
  - Next line: `#9FB6BF` (secondary)
  - Surrounding lines: `#6B8693` (tertiary, dimmed)
- Interaction: tap any line to seek to that timestamp; the panel auto-scrolls to keep the current line centered
- "ABOUT" / "CREDITS" variants reuse the same panel with a different label (artist facts, songwriters)

### Scrubber / Progress Bar
- Track: 4pt height, `rgba(255,255,255,0.16)`, 2pt radius
- Fill: `#00A8E1` (Amazon Cyan)
- Thumb: 13pt `#00A8E1` circle + `0 0 0 4pt rgba(0,168,225,0.2)` halo (halo grows to 6pt while dragging)
- Time labels: 11pt `#9FB6BF` tabular, elapsed left / remaining (negative) right
- Buffered: `rgba(255,255,255,0.28)` segment behind fill

### Album Art
- Player: 196pt square, 6pt corner radius, `0 18pt 44pt -16pt rgba(0,0,0,0.65)` shadow
- Shelf card art: 6pt radius (matches player)
- List-row art: 4pt radius
- HD / Ultra HD / Dolby Atmos badge overlaid bottom-left when applicable, glow-cyan text

### Cards / Shelves (Home)
- Background: `#122A33`
- Corner radius: 10pt
- Art: 6pt radius
- Title: Ember 18pt 700 `#FFFFFF`; subtitle 14pt 400 `#9FB6BF`
- Layout: horizontal-scroll shelves ("Recently played", "Your Soundtrack", "Stations for you")
- Pressed: `#1A3742`

### List Rows (song lists)
- Height: 58pt
- Leading: 44pt art (4pt radius) OR 24pt index `#9FB6BF`
- Primary: Ember 15pt 500 `#FFFFFF`
- Secondary: Ember 14pt 400 `#9FB6BF`
- Trailing: ⋮ overflow (22pt `#9FB6BF`); explicit "E" badge; HD badge if applicable
- Pressed: `#1A3742` full-row

### Chips (genre / mood / quality filters)
- Height: 32pt, 999pt radius
- Rest: `#1A3742`, text `#FFFFFF` Ember 13pt 600
- Selected: `#00A8E1` bg, `#FFFFFF` text
- Quality chip ("HD", "ULTRA HD"): `#25D1DA` text on transparent with `#25D1DA` border
- Horizontal scroll, 8pt gap

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#081218` with 0.5pt top border `#234653`
- Tabs (4): Home, Find, Podcasts, Library
- Icon: 22pt
- Active: `#25D1DA` (filled glyph, bright cyan)
- Inactive: `#6B8693` (outline glyph)
- No tint pill — bright cyan icon + label is the active signal
- Labels: Ember 10pt 500, always shown

**Player Top Bar**
- Height: 44pt
- Leading: chevron-down (dismiss to mini-player), 22pt `#FFFFFF`
- Center: source eyebrow ("PLAYING FROM PLAYLIST") 10pt 700 `#9FB6BF` uppercase, 0.7pt tracking
- Trailing: ⋮ overflow, 22pt `#FFFFFF`

**Player Footer Row**
- A row of 20pt secondary actions below transport: queue, lyrics-toggle, equalizer/Atmos, cast/devices
- Icons `#9FB6BF`; active state `#25D1DA`

**Mini-Player Bar (above tab bar)**
- Height: 56pt, background `#1A3742`, 8pt radius (floating) or full-bleed
- 40pt art (4pt radius) + title (13pt 600) + artist (11pt 400 `#9FB6BF`) + play/pause + next
- Tap to expand to full-screen player
- Thin cyan progress line at the bottom edge

### Input Fields

**Search Bar (Find tab)**
- Height: 44pt
- Background: `#122A33`
- Border: none
- Corner radius: 8pt
- Leading magnifier 18pt `#9FB6BF`
- Placeholder: "Songs, artists, podcasts & more" 15pt 400 `#6B8693`
- Focus: 1pt `#25D1DA` border + faint cyan glow

### Distinctive Components

**X-Ray Lyrics Panel** — see above. The single most identifying component; the synced cyan lyric line + "X-RAY" badge.

**Top-Down Player Gradient** — the teal lift toward `#16404C` behind the art that defines the player's depth.

**Time-of-Day Greeting** — the screen title changes ("Good morning" / "Good evening") — a small but characteristic Amazon Music touch.

**Quality Badges (HD / Ultra HD / Dolby Atmos)** — glow-cyan capability badges on art and rows, distinctive to Amazon Music's HD positioning.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 22, 28, 32, 44, 60
- Player vertical rhythm: art → 16pt → meta → 16pt → X-Ray panel → 16pt → scrubber → 12pt → controls
- Screen content inset: 18pt horizontal (slightly wider than the 16pt norm)
- Row vertical padding: 8pt

### Grid & Container
- iPhone: content at 18pt horizontal insets; player art 196pt centered
- Home shelves: horizontal-scroll carousels, 8pt gap, 18pt leading inset
- iPad: player art scales to 280pt; shelves show more cards; content max width 1000pt; the X-Ray panel can sit beside the art in landscape
- X-Ray panel: full content width minus 18pt insets

### Whitespace Philosophy
- **Art + lyrics are the center**: the player gives the cover and the X-Ray panel maximum prominence; chrome is compact
- **Tinted, not neutral**: dividers and surfaces are teal-tinted (`#234653`, not `#2C2C2E`) so the whole UI reads as one blue field
- **Dense lists, generous player**: 58pt list rows; the player breathes around the art and lyric panel

### Border Radius Scale
- Square (0pt): full-bleed gradients, backdrop
- Micro (2pt): scrubber track
- Subtle (4pt): list-row art, mini-player art, "X-RAY" badge
- Card-Art (6pt): album art (player + shelf cards)
- Panel (10pt): the X-Ray lyrics panel, sheets
- Comfortable (16pt): large modals
- Pill (999pt): CTA buttons, chips
- Circle (50%): play button, avatars

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, chrome, tab bar |
| Gradient (Level 1) | Top-down teal lift toward `#16404C` | Player background behind art |
| Floating (Level 2) | `0 18px 44px -16px rgba(0,0,0,0.65)` | Album art on the player |
| Glow (Level 2b) | `0 8px 22px -6px rgba(0,168,225,0.55)` | The cyan play button |
| Sheet (Level 3) | `0 -8px 28px rgba(0,0,0,0.55)` + scrim | Bottom sheets, queue |
| Modal Scrim | `rgba(0,0,0,0.55)` | Behind sheets and dialogs |

**Shadow Philosophy**: Depth on the player comes from the **gradient lift** plus the album-art drop shadow; the one luminous element is the **cyan glow on the play button**, which makes it the obvious focal action. Chrome is otherwise flat. The X-Ray panel uses a tinted fill + faint cyan border instead of a shadow to read as "raised" without adding visual noise.

### Motion
- **Player present/dismiss**: mini-bar expands upward into full screen over 300ms ease-out; reverse on chevron-down
- **X-Ray lyric scroll**: as playback advances, the lyric panel auto-scrolls so the current line stays centered; the line recolors tertiary→bright-cyan over 180ms ease-out; the previous line fades back to tertiary
- **X-Ray seek tap**: tapping a line scrubs playback to that timestamp; the tapped line pulses to bright cyan
- **Scrubber drag**: thumb halo grows 4→6pt instantly on touch-down; snaps back 150ms on release
- **Gradient settle**: on track change, the player gradient and art crossfade old→new over 350ms
- **Tab switch**: instant icon recolor to bright cyan (no slide, no pill)
- **Shelf scroll**: horizontal momentum scroll with rubber-band; cards snap softly
- **Haptic feedback**: light impact on play/pause, X-Ray line tap-to-seek, scrubber touch-down

## 7. Do's and Don'ts

### Do
- Use the deep teal-navy canvas `#0C1B22` — a tinted dark, NOT neutral grey
- Render the player on a top-down gradient lifting toward `#16404C` behind the art
- Make the play button a `#00A8E1` cyan circle with a white glyph and a cyan glow shadow
- Show the X-Ray lyrics panel below the metadata with the "X-RAY" badge and synced lines
- Highlight the currently-sung lyric line in bright cyan `#25D1DA`; dim surrounding lines
- Let tapping a lyric line seek playback to that moment
- Use teal-tinted greys for text/dividers (`#9FB6BF`, `#234653`) — not neutral greys
- Use Amazon Ember; if unavailable, fall back to Inter (it tracks Ember's metrics)
- Make the active tab bright cyan `#25D1DA` — brighter than the structural `#00A8E1`
- Use tabular numerals for all timestamps

### Don't
- Don't use a neutral grey or pure-black canvas — the teal tint is the brand thumbprint
- Don't make the play button white — cyan IS the action color in Amazon Music (unlike YT Music)
- Don't render lyrics as a plain unstyled list — the tinted X-Ray panel + cyan current line is the signature
- Don't use neutral `#A0A0A0` greys — secondary text is the teal-tinted `#9FB6BF`
- Don't substitute Ember for a serif or a geometric sans other than Inter
- Don't put a Material tint pill behind the active tab — the bright-cyan icon is the signal
- Don't drop the cyan glow on the play button — it's the player's luminous focal point
- Don't keep the lyric panel static during playback — it must auto-scroll and recolor the current line
- Don't introduce a second accent hue — Amazon Music is monochrome-teal + cyan
- Don't make the X-Ray badge low-contrast — it's `#042730` on bright `#25D1DA` for legibility

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Player art 168pt; X-Ray panel shows 3 lines |
| iPhone 13/14/15 | 390pt | Standard layout; art 196pt, X-Ray shows 4 lines |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in player top bar |
| iPhone 15/16 Pro Max | 430pt | Art 224pt; X-Ray shows 5 lines |
| iPad (portrait) | 768pt | Art 280pt centered; shelves show 3–4 cards |
| iPad (landscape) | 1024pt+ | Two-pane: art + transport left, X-Ray panel right; content max 1000pt |

### Dynamic Type
- Greeting, section header, body, row title, lyric line: full scale
- X-Ray badge, X-Ray label, tab labels, timestamps: FIXED (layout-sensitive)
- Lyric panel grows taller as the line scales, keeping the current line centered

### Orientation
- Portrait default everywhere
- iPad landscape splits the player into art/transport + a wider X-Ray lyrics column
- Lyrics full-screen mode (tap the X-Ray panel to expand): supports rotation, large centered lines

### Touch Targets
- Play button: 60pt glyph, 60pt+ hit
- Lyric lines (tap-to-seek): full line width, 44pt min height hit
- Skip / repeat / shuffle: 22–24pt glyph, 44pt hit
- List rows: 58pt tall, full-row tap; ⋮ overflow 44pt hit
- Tab bar icons: 22pt glyph, 49pt hit

### Safe Area Handling
- Top: player top bar and Dynamic Island respected
- Bottom: mini-player + tab bar + home indicator stacked, all safe-area aware
- The X-Ray panel never extends under the home indicator; the footer action row sits above it
- Lyrics full-screen: lines inset from notch and home indicator

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0C1B22`
- Surface 1: `#122A33`
- Surface 2 / mini-player: `#1A3742`
- Divider: `#234653`
- Gradient top (player): `#16404C`
- Amazon Cyan (structural accent): `#00A8E1`
- Cyan bright / glow (active lyric, active tab, X-Ray badge): `#25D1DA`
- Cyan pressed: `#0086B3`
- X-Ray panel fill: `rgba(0,168,225,0.07)`
- X-Ray panel border: `rgba(37,209,218,0.22)`
- Text primary: `#FFFFFF`
- Text secondary: `#9FB6BF`
- Text tertiary: `#6B8693`
- Success: `#2EC4B6`
- Error: `#FF6B6B`

### Example Component Prompts
- "Build the Amazon Music full-screen player in SwiftUI: background is a top-down `LinearGradient` from `#1C4A57` → `#163A45` → `#0F2730` → `#0C1B22`. Centered 196pt square album art, 6pt corners, `0 18pt 44pt -16pt rgba(0,0,0,0.65)` shadow. Below: track name Ember 26pt weight 700 `#FFFFFF`, artist Ember 14pt 400 `#9FB6BF`, trailing a 26pt cyan `#25D1DA` add-to-library plus icon."

- "Create the Amazon Music X-Ray lyrics panel: `rgba(0,168,225,0.07)` fill, 1pt `rgba(37,209,218,0.22)` border, 10pt corners, 14pt/16pt padding. Header: an 'X-RAY' badge (`#25D1DA` bg, `#042730` text, Ember 9pt 800, 0.5pt tracking, 4pt radius) followed by a 'LYRICS' label (`#9FB6BF`, Ember 10pt 700, 0.6pt tracking, uppercase). Lyric lines in Ember 15pt 600 line-height 1.55: current line `#25D1DA`, next line `#9FB6BF`, surrounding lines `#6B8693`. Tapping a line seeks playback to its timestamp; the panel auto-scrolls to keep the current line centered."

- "Render the Amazon Music play button: a 60pt circle filled `#00A8E1` with a white play/pause glyph at 24pt and a `0 8pt 22pt -6pt rgba(0,168,225,0.55)` cyan glow shadow. Pressed: `#0086B3` + scale 0.97. NOTE: cyan IS the action color here — do not make it white."

- "Build the Amazon Music scrubber: 4pt track `rgba(255,255,255,0.16)` 2pt radius, fill `#00A8E1`, 13pt `#00A8E1` thumb with a `0 0 0 4pt rgba(0,168,225,0.2)` halo that grows to 6pt while dragging. Time labels Ember 11pt 400 `#9FB6BF` tabular figures, elapsed left, remaining (negative) right."

- "Create the Amazon Music bottom tab bar: 56pt + safe area, background `#081218`, 0.5pt top border `#234653`. Four tabs — Home, Find, Podcasts, Library — 22pt icons, active `#25D1DA` filled glyph (bright cyan), inactive `#6B8693` outline glyph, NO tint pill. Labels Ember 10pt 500 always shown."

- "Render the Amazon Music Home shelf: a time-of-day greeting ('Good evening') in Ember 32pt 800 `#FFFFFF`, then horizontal-scroll carousels ('Recently played', 'Your Soundtrack', 'Stations for you'). Each card: `#122A33` bg, 10pt corners, 6pt-radius art, title Ember 18pt 700 `#FFFFFF`, subtitle Ember 14pt 400 `#9FB6BF`, 8pt gap between cards, 18pt leading inset."

### Iteration Guide
1. The canvas is deep teal-navy `#0C1B22` — a tinted dark, NOT neutral grey; this is the brand thumbprint
2. The player sits on a top-down gradient lifting toward `#16404C` behind the art
3. The play button is a `#00A8E1` cyan circle with a white glyph + cyan glow — cyan IS the action
4. The X-Ray lyrics panel is the signature: tinted fill, cyan border, "X-RAY" badge, synced lines
5. The currently-sung lyric line is bright cyan `#25D1DA`; surrounding lines dim to tertiary
6. Tapping a lyric line seeks playback to that moment; the panel auto-scrolls
7. Secondary text & dividers are teal-tinted (`#9FB6BF`, `#234653`) — never neutral grey
8. Amazon Ember type; Inter is the only acceptable fallback (it tracks Ember's metrics)
9. The active tab is bright cyan `#25D1DA` — brighter than the structural `#00A8E1`
10. Use tabular numerals for timestamps; keep the cyan glow on the play button
