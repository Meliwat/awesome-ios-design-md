# Design System Inspiration of ESPN (iOS)

## 1. Visual Theme & Atmosphere

ESPN's iOS app is a live scoreboard that happens to also be a newsroom. Everything in the design serves one goal: **get the score in front of the user in under a second**. The canvas is a near-black `#0E0F11` (deliberately not pure black, so the scores ticker and game cards lift cleanly off it). The first thing on screen is a **horizontally scrolling scores ticker** — a strip of compact cards, one per game, each showing league, two team abbreviations with logos, scores in tabular numerals, and a status pill (LIVE Q3 / FINAL / 7:05 ET). Below it flows the **SportsCenter feed**: a mix of live game cards, breaking-news stories, highlights, and analysis. The app is dense, fast, and information-first — it is engineered for a fan glancing at it 30 times a day.

The single brand accent is **ESPN Red** (`#D50A0A` — with a brighter `#CC0000` for emphatic CTAs and links). It is used with discipline: the ESPN logo block, the primary "Watch Live" / "Watch on ESPN+" call-to-action, the active bottom-tab indicator dot, story category tags ("BREAKING", "ANALYSIS"), and section accents. Red is never decoration — it means "ESPN brand", "watch this", or "this is hot". Live games get a hotter, brighter red (`#FF1A1A`) with a pulsing dot so a glance instantly separates in-progress from finished. Final scores dim the loser to gray; the **winning team's score turns green** (`#1FAA59`) so a result is legible without reading a single word.

The signature surfaces are the **scores feed** (ticker + game cards + SportsCenter stories) and the **game detail screen** (a big score header, team records, a live clock, drive/play info, and tabbed sections — Gamecast / Box Score / Plays / News). The defining components are the **game card** (league header, two team rows with logo + name + record + big tabular score, a footer with the clock and an ESPN+ watch link) and the **live status badge** (a pulsing-dot LIVE pill). No other sports app packs this much live state into a glanceable card.

Typography is a **bold condensed-leaning sans** — ESPN's brand face is a custom "ESPN" type; the closest free analog is **Archivo** (with its expanded weight range and slightly condensed feel). Hierarchy is built for scanning: enormous heavy headlines and scores, tight team names, small dense metadata. Weights concentrate at 400 (body/meta), 700 (team names, story titles), 800 (section headers), and 900 (headlines, screen titles, scores, status badges). **Scores always use tabular (monospaced) numerals** so digits stay column-aligned across every card — a non-negotiable for a scoreboard. On iOS, Archivo falls back to SF Pro (use SF Pro's `.heavy`/`.black` and a tabular numeral feature for scores).

**Key Characteristics:**
- Near-black canvas (`#0E0F11`) — not pure black, so the ticker and cards lift cleanly
- Horizontal scores ticker pinned near the top — the fastest path to "what's the score"
- SportsCenter feed — interleaved live game cards, breaking news, highlights, analysis
- Single accent — ESPN Red (`#D50A0A` / bright `#CC0000`) for logo, CTA, active-tab dot, tags
- Live state is a hotter red (`#FF1A1A`) with a pulsing dot; finals dim to gray
- Winning team's score turns green (`#1FAA59`) — a result readable at a glance
- Game card — league header, two team rows (logo + name + record + score), clock + ESPN+ footer
- Tabular numerals on every score — digits stay column-aligned across cards
- Archivo (ESPN-style bold condensed sans) — built for scanning, not reading
- Five-tab bottom bar: Home / Scores / Watch / ESPN BET / More, active tab marked by a red dot
- Dense, information-first layout — engineered for the 30-glances-a-day fan

## 2. Color Palette & Roles

### Primary (Interactive)
- **ESPN Red** (`#D50A0A`): The core brand accent — logo block, active-tab indicator dot, section accents, primary surfaces of brand identity.
- **Red Bright** (`#CC0000`): Emphatic CTAs and links — "Watch on ESPN+", story category tags, inline links.
- **Red Pressed** (`#A50808`): Pressed state of the red CTA.

### Canvas & Surfaces (Dark — the primary mode)
- **Near-Black Canvas** (`#0E0F11`): Primary canvas — not pure black, so cards and the ticker lift.
- **Surface 1** (`#18191B`): Game-card body, story containers.
- **Surface 2** (`#212327`): Ticker chips, card headers, score rows, search field.
- **Surface 3** (`#2B2D31`): Pressed rows, "FINAL" badge fill, chips.
- **Divider** (`#2A2C30`): Hairline dividers between team rows, stories, and sections.

> ESPN's app is **dark-first**. A light theme exists in some surfaces but the scoreboard identity (near-black ground, red accent, green-winner) is defined in dark; this spec documents the dark system.

### Text
- **Text Primary** (`#FFFFFF`): Headlines, team names, scores, screen titles — pure white.
- **Text Secondary** (`#9AA0A6`): Metadata (league · time · read-length), team records, losing-team scores, inactive tab labels.
- **Text Tertiary** (`#6B7075`): Disabled actions, "FINAL" label text, very low-emphasis captions.

### Brand & Status Accents
| Role | Color | Usage |
|------|-------|-------|
| ESPN Red | `#D50A0A` | Logo block, active-tab dot, section accents |
| Red Bright | `#CC0000` | "Watch on ESPN+" link, category tags, inline links |
| Live Red | `#FF1A1A` | LIVE badge fill + pulsing dot — in-progress games |
| Win Green | `#1FAA59` | The winning team's score on a finished game |
| Rank Gold | `#F2C200` | Top-25 / ranking numerals, "#1 seed" emphasis |

### Semantic
- **Error Red** (`#E5484D`): Network/feed-failure banner, destructive confirm (distinct from brand red).
- **Success / Win** (`#1FAA59`): Winning score, "Following" confirmation toast.
- **Live / Alert** (`#FF1A1A`): Live games, breaking-alert banner pulse.
- **Pulse Dot**: a small filled circle that animates opacity 1 → 0.4 → 1 over ~1.2s beside the LIVE label.

## 3. Typography Rules

### Font Family
- **Primary**: ESPN's brand face is a proprietary condensed sans. The closest free analog is **Archivo** (by Omnibus-Type, SIL OFL) — a grotesque with a slightly condensed feel and a wide weight range (400–900), well-suited to dense sports UI.
- **Heavy Display**: `Archivo` weight **900 (Black)**, occasionally italic — used for headlines, scores, and status badges; the energetic sports voice.
- **Numerals**: Scores and clocks **must** use tabular (monospaced) figures — `font-variant-numeric: tabular-nums` / SF Pro's `.monospacedDigit` — so digits stay column-aligned.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Archivo', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: Without Archivo bundled, fall back to SF Pro `.heavy`/`.black` and apply `.monospacedDigit()` on all scores/clocks.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Archivo | 32pt | 900 | 1.1 | -0.5pt | "Scores", "Watch" large nav titles |
| Headline | Archivo | 26pt | 900 | 1.15 | -0.3pt | Lead-story headline |
| Section Header | Archivo | 22pt | 800 | 1.2 | -0.2pt | "Top Headlines", "Today's Games" |
| Team Name | Archivo | 18pt | 700 | 1.2 | 0pt | "Los Angeles Lakers" on the game card |
| Body | Archivo | 16pt | 400 | 1.5 | 0pt | Article body, game summary |
| Story Title | Archivo | 15pt | 700 | 1.3 | 0pt | Feed story headline (compact) |
| Score (large) | Archivo | 26pt | 900 | 1.0 | 0pt | Game-card team score — **tabular** |
| Score (ticker) | Archivo | 13pt | 700 | 1.0 | 0pt | Ticker team score — **tabular** |
| Meta Line | Archivo | 14pt | 400 | 1.4 | 0pt | "NBA · 14m ago · 3 min read" |
| Team Record | Archivo | 12pt | 400 | 1.3 | 0pt | "48–34 · Home" |
| Button Label | Archivo | 16pt | 800 | 1.0 | 0.3pt | "Watch Live", "Follow Team" |
| Status Badge | Archivo | 11pt | 900 | 1.0 | 0.5pt | LIVE / FINAL / 7:05 ET (uppercase) |
| Category Tag | Archivo | 10pt | 900 | 1.0 | 0.5pt | "BREAKING", "ANALYSIS" (uppercase, red) |
| Tab Label | Archivo | 10pt | 700 | 1.0 | 0.1pt | Bottom-tab labels |

### Principles
- **Tabular numerals everywhere there's a number**: scores, clocks, records, rankings — non-negotiable for a scoreboard; digits never shift.
- **Scanning rhythm, not reading rhythm**: enormous 900-weight headlines and scores, tight team names, dense meta — built for a 1-second glance.
- **Uppercase only for badges, category tags, and league labels** — never for headlines or body.
- **Body and meta stay 400** — only headlines, team names, scores, and CTAs get weight.
- **Metadata is secondary gray** (`#9AA0A6`) — league/time/read-length never compete with the headline or score.
- **Dynamic Type**: headlines, body, meta scale; ticker scores, status badges, tab labels, the pulse dot stay FIXED (the ticker is a fixed-height strip).

## 4. Component Stylings

### Buttons

**Primary Button ("Watch Live")**
- Shape: Rounded rectangle, 6pt corner radius (ESPN runs tighter radii than most — sharp, sporty)
- Background: `#D50A0A` (ESPN Red)
- Text + leading play glyph: `#FFFFFF`, Archivo 16pt weight 800, 0.3pt tracking
- Padding: 14pt vertical, 28pt horizontal (or full-width on detail screens)
- Pressed: background `#A50808` + scale 0.98
- Disabled: background `#212327`, text `#6B7075`

**Secondary / Outline Button ("Follow Team")**
- Background: transparent
- Border: 1.5pt `rgba(255,255,255,0.35)`
- Text: `#FFFFFF`, Archivo 14pt weight 700
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 6pt
- Pressed: background `rgba(255,255,255,0.06)`; once following, fills `#D50A0A`

**Ghost Button ("Gamecast")**
- Background: `#212327`
- Text: `#FFFFFF`, Archivo 14pt weight 700
- Corner radius: 6pt
- Padding: 12pt vertical, 20pt horizontal

**Text Button ("Full box score")**
- Background: none
- Text: `#CC0000` (Red Bright), Archivo 14pt weight 800
- No underline
- Pressed: opacity 0.6

### Game Card (the core atom)

The single most important ESPN component — a glanceable summary of one game.

- Container: `#18191B`, 12pt corner radius, 1pt `#2A2C30` border
- **Header**: `#212327` strip, league/series label (Archivo 11pt weight 800 `#9AA0A6`, uppercase, 0.5pt tracking) on the left; status on the right — LIVE pill (`#FF1A1A` + pulsing dot) / "FINAL" (gray) / start time
- **Team row** (×2): team logo (30pt circle) + name (Archivo 18pt weight 700 `#FFFFFF`) + record (Archivo 12pt weight 400 `#9AA0A6`) on the left; big score (Archivo 26pt weight 900, **tabular**) on the right
  - Live/leading or winning team: score `#FFFFFF` (or `#1FAA59` if the game is final and they won)
  - Trailing/losing team: score `#9AA0A6` (dimmed)
- 1pt `#2A2C30` divider between the two team rows
- **Footer**: `#0E0F11`-toned strip, top 1pt divider — live clock (Archivo 13pt weight 700 `#FFFFFF`, e.g. "Q3 · 4:12") on the left; "Watch on ESPN+" link (Archivo 12pt weight 800 `#CC0000` + play glyph) on the right
- Pressed: scale 0.98, 150ms ease-out → pushes the game detail screen

### Scores Ticker (signature component)

- A horizontally scrolling strip pinned near the top, ~fixed height, 1pt `#2A2C30` bottom border
- Each chip: `#212327`, 8pt corner radius, ~116pt wide, 8–10pt padding
- Chip header: league (Archivo 10pt weight 800 `#9AA0A6`) + status (LIVE `#FF1A1A` pill / "FINAL" gray / start time)
- Two team lines: abbreviation + 16pt logo dot on the left, score (Archivo 13pt weight 700, **tabular**) on the right
- Winning/leading team line `#FFFFFF`; other line `#9AA0A6`
- Tapping a chip opens the game detail screen
- Horizontal scroll only; never wraps — the ticker is a one-line glance

### SportsCenter Story Row

- Container: full width, 14pt vertical padding, 1pt `#2A2C30` bottom divider
- Thumbnail: 96×72pt, 8pt corner radius (image or sport-tinted placeholder)
- Category tag: Archivo 10pt weight 900 uppercase `#CC0000`, 0.5pt tracking — "BREAKING" / "ANALYSIS" / "HIGHLIGHT"
- Title: Archivo 15pt weight 700 `#FFFFFF`, 4pt below tag, 2-line clamp
- Meta: Archivo 12pt weight 400 `#9AA0A6`, 6pt below title — "NBA · 14m ago · 3 min read"
- 12pt gap between thumbnail and text column

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area (≈64pt total)
- Background: `rgba(14,15,17,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A2C30`
- Tabs (5): Home, Scores, Watch, ESPN BET, More
- Icon size: 22pt
- Active: glyph + label `#FFFFFF`, plus a small `#D50A0A` indicator dot below the label
- Inactive: glyph + label `#6B7075`
- Labels: Archivo 10pt weight 700, always shown
- The red dot (not a tint pill) is the active marker — ESPN keeps the icon white and uses red only as the indicator

**Top Bar (Home / feed)**
- Height: 44pt + safe area
- Background: `#0E0F11`
- Leading: ESPN red logo block
- Trailing: search glyph + alerts bell, 22pt, `#FFFFFF`
- The scores ticker sits directly beneath the top bar (pinned above the scrolling feed)

**Top Bar (game detail)**
- Compact: back chevron + matchup label + share + alert-toggle
- Below: the big score header (team logos, names, big tabular scores, live clock) then a tabbed control (Gamecast / Box Score / Plays / News)

### Input Fields

**Search Field**
- Height: 44pt
- Background: `#212327`
- Corner radius: 6pt
- Leading icon: magnifier 16pt `#9AA0A6`
- Placeholder: "Search teams, players, news" Archivo 15pt weight 400 `#6B7075`
- Text: `#FFFFFF`
- Focus: 1.5pt `#D50A0A` border

**Tabbed Control (game detail: Gamecast / Box Score / Plays / News)**
- A row of items, Archivo 14pt weight 800 — `#9AA0A6` inactive, `#FFFFFF` active
- Active indicator: 3pt `#D50A0A` underline beneath the active item, slides 200ms ease-out
- Sits above a 1pt `#2A2C30` divider

### Distinctive Components

**Live Status Badge**
- Pill, `#FF1A1A` fill, Archivo 11pt weight 900 uppercase `#FFFFFF`, 0.5pt tracking, 4×9pt padding, 3pt radius
- Leading 6pt white pulsing dot (opacity 1 → 0.4 → 1 over ~1.2s)
- Variants: **LIVE** (+ period, e.g. "LIVE Q3"), **FINAL** (`#2B2D31` fill, `#9AA0A6` text, no dot), **start time** (transparent + 1pt `rgba(255,255,255,0.4)` outline, white text)

**Winning-Score Treatment**
- On a finished game, the winning team's score is `#1FAA59` (Win Green); the losing team's score is `#9AA0A6`
- The single most ESPN-specific data treatment — a result is legible without reading any text

**Scores Ticker Chip**
- See Scores Ticker above — the compact, fixed-height, horizontally scrolling game summary

**Category Tag**
- Inline, no background, Archivo 10pt weight 900 uppercase `#CC0000`, 0.5pt tracking — "BREAKING" / "ANALYSIS" / "HIGHLIGHT" / "INSIDER"

**Pulsing Live Dot**
- A 6–7pt filled circle (`#FF1A1A` standalone, `#FFFFFF` inside a LIVE pill) animating opacity 1 → 0.4 → 1 over ~1.2s ease-in-out, infinitely — the universal "this is happening now" cue

**Rank Numeral**
- Top-25 / seeding numbers in `#F2C200` (Rank Gold), tabular, e.g. "#3" before a team name in college contexts

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64
- Screen horizontal inset: 16pt (tighter than most — ESPN is dense)
- Ticker chip gap: 8pt
- Card / story vertical gap: 16pt

### Grid & Container
- iPhone: 16pt horizontal insets; the scores ticker is full-bleed horizontal scroll
- Feed: single-column list of game cards + story rows at 16pt insets
- Ticker: fixed-height horizontal scroll, chips ~116pt wide, 8pt gap, first chip flush to 12pt
- iPad: 2-column feed (cards + stories) at wider insets; game detail uses a 2-pane (score header + tabs)

### Whitespace Philosophy
- **Information density is the feature**: ESPN intentionally packs more per screen than a typical app — fans want maximum signal per glance
- **Near-black as separator**: the `#0E0F11` ground separates `#212327` chips and `#18191B` cards without heavy borders
- **Tabular alignment is sacred**: scores and clocks line up in columns across every card and ticker chip
- **Single-accent restraint**: red appears in ≤4 roles per screen (logo, CTA, active-tab dot, tags) — discipline keeps it meaningful; live red and win green are *status*, not accents

### Border Radius Scale
- Square (0pt): Dividers, full-bleed strips
- Tight (3pt): Status badges, category chips
- Standard (6pt): Buttons, search field, tabbed underline geometry
- Card (8pt): Ticker chips, story thumbnails
- Comfortable (12pt): Game cards, bottom sheets
- Large (16pt): Modals
- Pill (500pt): Live/status pills, filter chips
- Circle (50%): Team logos, avatars, the pulse dot

## 6. Depth & Elevation

ESPN is mostly flat — depth comes from surface-color steps and the live pulse, not heavy shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed background, story rows on the near-black canvas |
| Surface Step (Level 1) | Background lift `#0E0F11` → `#18191B` → `#212327` (no shadow) | Game cards, ticker chips, card headers |
| Pinned Ticker (Level 1.5) | 1pt `#2A2C30` bottom border, no shadow | The scores ticker pinned above the feed |
| Floating (Level 2) | `0 4px 16px rgba(0,0,0,0.5)` | Alert banners, popovers, the now-playing mini bar |
| Sheet (Level 3) | `0 -6px 28px rgba(0,0,0,0.6)` | Action sheets, share sheet, league filter |
| Modal Overlay | `rgba(0,0,0,0.6)` | Dim behind sheets and modals |

**Shadow Philosophy**: ESPN uses surface-color steps for structure (a game card is one step up from the canvas, its header another step) and reserves shadows for genuinely floating UI (alert banners, sheets, the persistent watch mini-bar). The *live pulse* is ESPN's signature "depth" — motion, not elevation, draws the eye to what's happening now.

### Motion
- **Live pulse**: the LIVE dot animates opacity 1 → 0.4 → 1 over ~1.2s ease-in-out, infinitely, on every in-progress game
- **Score update**: when a score changes, the new number cross-fades + does a subtle 1.06 scale pop over 200ms (the scoreboard "tick")
- **Ticker auto-advance (optional)**: on the feed, a subtle nudge can scroll the ticker to surface the next live game; manual horizontal scroll always overrides
- **Tabbed underline**: the red underline slides to the tapped tab over 200ms ease-out
- **Card press**: scale 0.98 in 150ms ease-out → pushes the game detail
- **Tab switch**: instant content swap; the red active-dot cross-fades in over 120ms
- **Pull to refresh**: a red spinner; refetches the ticker + feed
- **Status transition**: when a game ends, the LIVE pill cross-fades to "FINAL" (~250ms) and the winner's score animates color → green over 300ms
- **Breaking alert**: a top banner slides down 250ms with a brief red pulse, auto-dismisses or is swiped away
- **Haptic feedback**: light impact on Follow toggle, tab change, and pull-to-refresh; medium impact on a tracked team's score change (the "your team scored" buzz)

## 7. Do's and Don'ts

### Do
- Use near-black (`#0E0F11`) as the canvas — NOT pure black; cards and the ticker must lift off it
- Pin a horizontal scores ticker near the top — it's the fastest path to "what's the score"
- Use ESPN Red (`#D50A0A` / bright `#CC0000`) as the only brand accent — logo, CTA, active-tab dot, tags
- Use a hotter red (`#FF1A1A`) + a pulsing dot for LIVE games; dim finals to gray
- Turn the winning team's score green (`#1FAA59`) on finished games — a result readable at a glance
- Use tabular numerals on every score, clock, record, and ranking — columns must align
- Build the game card with league header + two team rows (logo/name/record/score) + clock & ESPN+ footer
- Use Archivo (or SF Pro `.black`) at heavy weights for headlines, scores, and badges
- Mark the active tab with a small red dot — keep the icon white
- Keep the layout dense and information-first — fans want maximum signal per glance

### Don't
- Don't use pure black (`#000000`) — ESPN's ground is near-black so cards have contrast
- Don't introduce a second brand accent — red is it; live red and win green are *status*, not accents
- Don't use proportional numerals for scores — digits will shift and the scoreboard breaks
- Don't hide or bury the scores ticker — it is the product's spine
- Don't use a tint pill for the active tab — ESPN uses a red dot under a white icon
- Don't let metadata (league/time/read-length) use white — it's secondary gray `#9AA0A6`
- Don't drop the live pulse — motion is how ESPN says "this is happening now"
- Don't use heavy drop-shadows for structure — use surface-color steps; reserve shadow for floating UI
- Don't dim the winning score — winners are white or green, never gray
- Don't over-space the layout — ESPN's density is intentional; airy layouts feel un-ESPN
- Don't animate aggressively elsewhere — only the pulse and the score-pop are lively; the rest is calm

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Ticker chips ≈ 110pt; single-column feed; tab labels stay |
| iPhone 13/14/15 | 390pt | Standard layout; ticker chips ≈ 116pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by the top bar; ticker sits below it |
| iPhone 15/16 Pro Max | 430pt | Ticker chips ≈ 124pt; larger game cards |
| iPad (portrait) | 768pt | 2-column feed; game detail = score header + tabbed pane |
| iPad (landscape) | 1024pt+ | 2–3 column feed; persistent ticker; multi-pane game detail |

### Dynamic Type
- Scales: headlines, section headers, body, story titles, meta
- Fixed: ticker scores + chips, status badges, tab labels, the pulse dot, the game-card score size (the card is a fixed-rhythm scoreboard)
- Long headlines wrap to 2 lines; story titles clamp to 2 lines

### Orientation
- Portrait-primary for the feed; the video player (Watch / ESPN+) is landscape full-screen
- iPad supports landscape with a wider multi-column feed and a persistent ticker

### Touch Targets
- Primary CTA: 48pt+ tall
- Tab icons: 22pt glyph, 49pt+ hit area
- Ticker chip: full-chip tap, ≥56pt tall
- Game card: full-card tap, ≥120pt tall
- Story row: full-row tap, ≥72pt tall

### Safe Area Handling
- Top: top bar + pinned ticker respect the safe area and the Dynamic Island
- Bottom: bottom tab bar + home indicator respected; the persistent watch mini-bar (if playing) sits above the tab bar
- The ticker is pinned (not under the status bar) — it stays glanceable while the feed scrolls beneath it
- Sides: 16pt content inset; the ticker is full-bleed horizontal scroll

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0E0F11` (near-black — NOT pure black)
- Surface 1 (card body): `#18191B`
- Surface 2 (ticker chip / header): `#212327`
- Surface 3 (pressed / FINAL): `#2B2D31`
- Divider: `#2A2C30`
- Text primary: `#FFFFFF`
- Text secondary: `#9AA0A6`
- Text tertiary: `#6B7075`
- ESPN Red (brand accent): `#D50A0A`
- Red Bright (CTA / links / tags): `#CC0000`
- Live Red (LIVE pill + pulse): `#FF1A1A`
- Win Green (winning score): `#1FAA59`
- Rank Gold: `#F2C200`
- Error: `#E5484D`

### Example Component Prompts
- "Build the ESPN game card in SwiftUI: a `#18191B` container, 12pt corner radius, 1pt `#2A2C30` border. Header: a `#212327` strip with 'NBA · WESTERN FINALS · GAME 5' in Archivo 11pt weight 800 `#9AA0A6` uppercase on the left, and a LIVE pill on the right (`#FF1A1A` fill, white 6pt pulsing dot, 'LIVE' in Archivo 11pt weight 900 white, 3pt radius). Two team rows: 30pt logo + name (Archivo 18pt weight 700 `#FFFFFF`) + record (Archivo 12pt weight 400 `#9AA0A6`) on the left, big score (Archivo 26pt weight 900, monospacedDigit) on the right — leading team white, trailing team `#9AA0A6`. 1pt `#2A2C30` divider between rows. Footer: top 1pt divider, 'Q3 · 4:12' in Archivo 13pt weight 700 `#FFFFFF` left, 'Watch on ESPN+' in Archivo 12pt weight 800 `#CC0000` + play glyph right."

- "Create the ESPN scores ticker: a horizontally scrolling `ScrollView`, fixed height, 1pt `#2A2C30` bottom border. Each chip: `#212327`, 8pt radius, ~116pt wide, 8pt padding. Chip header: league in Archivo 10pt weight 800 `#9AA0A6` + a status pill (LIVE `#FF1A1A` / 'FINAL' gray / start time). Two lines: 'LAL' + 16pt logo dot + score (Archivo 13pt weight 700, monospacedDigit) — winning line `#FFFFFF`, other `#9AA0A6`. Tapping a chip pushes the game detail."

- "Render the ESPN winning-score treatment: on a finished game, the winning team's score is `#1FAA59` (Win Green) and the losing team's score is `#9AA0A6`. Both use Archivo 26pt weight 900 with `.monospacedDigit()` so the columns align."

- "Build the ESPN primary CTA: a 6pt-corner-radius button, background `#D50A0A`, a 16pt white play triangle + 'Watch Live' in Archivo 16pt weight 800 `#FFFFFF` with 0.3pt tracking, 8pt gap. 14pt vertical / 28pt horizontal padding. Pressed: background `#A50808` + scale 0.98."

- "Create the ESPN live status badge: a 3pt-radius pill, `#FF1A1A` fill, a 6pt white dot that pulses opacity 1 → 0.4 → 1 over 1.2s infinitely, then 'LIVE Q3' in Archivo 11pt weight 900 uppercase `#FFFFFF`, 0.5pt tracking, 4×9pt padding. The FINAL variant: `#2B2D31` fill, `#9AA0A6` text, no dot. The upcoming variant: transparent + 1pt `rgba(255,255,255,0.4)` border, white text."

- "Create the ESPN bottom tab bar: 50pt + safe area, background `rgba(14,15,17,0.96)` with blur(20px) saturate(180%), 0.5pt top border `#2A2C30`. Five tabs — Home, Scores, Watch, ESPN BET, More — 22pt glyphs with Archivo 10pt weight 700 labels always shown. Active: white glyph + white label + a 4pt `#D50A0A` dot below the label. Inactive: `#6B7075` glyph + label. No tint pill — the red dot is the marker."

### Iteration Guide
1. Canvas is near-black `#0E0F11` — NOT pure black; the ticker and cards must lift off it
2. ESPN Red `#D50A0A` (bright `#CC0000`) is the only brand accent — logo, CTA, active-tab dot, tags
3. LIVE games use a hotter red `#FF1A1A` + a pulsing dot; finals dim to gray — status, not accent
4. The winning team's score turns green `#1FAA59` on finished games — a result readable at a glance
5. Every score / clock / record / ranking uses tabular numerals — columns must align
6. The horizontal scores ticker is pinned near the top — it's the product's spine, never buried
7. The game card is the core atom: league header + two team rows (logo/name/record/score) + clock & ESPN+ footer
8. Archivo (or SF Pro `.black`) at heavy weights for headlines, scores, and badges — built for scanning
9. The active tab is marked by a small red dot under a white icon — never a tint pill
10. Density is the feature — ESPN packs maximum signal per glance; airy layouts feel un-ESPN
