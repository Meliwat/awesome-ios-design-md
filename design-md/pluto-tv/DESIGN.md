# Design System Inspiration of Pluto TV (iOS)

## 1. Visual Theme & Atmosphere

Pluto TV's iOS app is, more than almost any other streaming app, **one screen**: the **Live TV channel guide (EPG)**. Where Netflix and Tubi lead with poster art, Pluto leads with a grid that looks like cable television rebuilt for a phone — because that's exactly what Pluto is: free, ad-supported *linear* TV with hundreds of always-on channels. The canvas is a deep cool navy (`#0B0F1F`), not pure black, evoking the soft glow of a TV in a dark room. Sitting on that navy is **Pluto Blue (`#0048FF`)** — a saturated, electric, almost-cobalt blue used for the wordmark block, primary CTAs, and the active category pill — paired with the brand's signature **yellow (`#FFE100`)**, which is reserved almost exclusively for the *single most important idea in the app*: what is **on now**.

The signature screen is the **EPG grid**. It has three fixed parts: (1) a **mini-player** pinned at the top that streams whatever channel is currently selected, with a `LIVE` badge and the current program's title; (2) a **sticky time-bar** marking 30-minute increments that stays put as you scroll; and (3) the **grid body** — a vertically-scrolling list of channel rows, each with a **fixed left column** (channel number + channel logo tile) and a **horizontally-scrolling timeline of program cells** to its right. The cell airing right now — the **"On Now" cell** — is the visual anchor of the entire UI: it lifts to a slightly brighter surface (`#1C2440`) and gets a **3pt yellow (`#FFE100`) left border**. That yellow bar is Pluto's most recognizable design signature; your eye is trained to scan down the left edge of the grid for it. Tapping any cell either tunes the mini-player to that channel (On Now) or opens a schedule/detail sheet (future programs).

The atmosphere is **utilitarian, channel-surfing, and proudly retro-broadcast**. Pluto isn't trying to feel like a premium SVOD app; it's trying to feel like *free TV you can flip through*. The category pills across the top (All, Featured, Movies, News, Sports, Comedy…) work like genre "tiers" on a cable box. The secondary surfaces — On Demand rows, channel detail, the player — are more conventional streaming layouts, but they all defer to the guide. Typography is a clean humanist grotesque (Manrope is the closest free analog): heavy 700–800 weights for titles and channel names, compact 12–13pt for program cells, and ALL-CAPS tracked micro-labels for `LIVE`/`ON NOW`. The result reads like a modern, well-organized TV guide channel — dense, scannable, and instantly familiar to anyone who's ever held a remote.

**Key Characteristics:**
- Deep cool-navy canvas (`#0B0F1F`) — late-night TV glow, not pure black; dark-only, no light mode
- The product *is* the EPG channel guide — a grid, not a poster wall
- Pluto Blue (`#0048FF`) for the wordmark, primary CTAs, active category pill
- Signature yellow (`#FFE100`) reserved for "on now / live" emphasis and the active tab
- Pinned mini-player on top of the guide — streams the selected channel with a LIVE badge
- Sticky 30-minute time-bar that stays fixed while the grid scrolls
- Fixed left channel column (channel number + logo tile) + horizontally-scrolling program timeline
- **"On Now" cell** = brighter surface (`#1C2440`) + 3pt yellow left border — the app's #1 design signature
- Red `#FF3B5C` dot + "LIVE" label as the universal live indicator
- Category-tier pills across the top (All / Featured / Movies / News / Sports / Comedy)
- Bottom tabs: Live TV / On Demand / Search / My Pluto / Account — active icon in yellow

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Pluto Blue** (`#0048FF`): Primary brand color — wordmark block, primary CTA ("Watch Live"), active category pill, selected channel highlight.
- **Pluto Blue Pressed** (`#0039CC`): Pressed state of blue CTAs.
- **Pluto Blue Bright** (`#2C6BFF`): Hover/focus lift of blue, secondary blue accents, links on dark surfaces.
- **Pluto Yellow** (`#FFE100`): The "on now / live emphasis" color — On Now cell left border, active tab icon, "Start Watching" highlight CTA. Text on yellow is `#0B0F1F`.
- **Pluto Cyan** (`#00C2FF`): Tappable text links, channel-name accents, "Full Schedule".

### Canvas & Surfaces (Dark — the only mode)
- **Canvas Navy** (`#0B0F1F`): Primary app background; also the gap-line color between program cells.
- **Surface 1** (`#141A2E`): Channel-column tiles, idle program cells, list rows, sheets.
- **Surface 2** (`#1C2440`): The **On Now** cell, elevated cards, pressed rows, the now-playing bar.
- **Surface 3** (`#25304F`): Highest elevation — popovers, modals, dropdowns.
- **Divider** (`#2A3556`): Hairline grid lines (row separators, channel-column border, time-bar underline).

### Text
- **Text Primary** (`#FFFFFF`): Titles, channel names, On Now program titles, body.
- **Text Secondary** (`#9DA8C7`): Metadata, idle category pills, schedule times — a cool slate.
- **Text Tertiary** (`#6B7595`): Channel numbers, time-bar labels, future-program times, disabled.
- **Text On-Yellow** (`#0B0F1F`): Text on the yellow highlight CTA / chips.
- **Text On-Blue** (`#FFFFFF`): Text on blue CTAs.

### Semantic & Accent
- **Live Red** (`#FF3B5C`): The universal LIVE indicator dot + "LIVE" label (player, mini-player, now-playing bar).
- **Success Green** (`#21D07A`): "Added to Watchlist", reminder set.
- **Error Red** (`#FF4A6E`): Playback errors, failed actions, destructive confirms.
- **Channel Logo Tints**: Channel logo tiles use per-channel brand gradients (e.g., Action `#0048FF→#00C2FF`, Movies `#FF6B2C→#FF2C6B`, Sports `#21D07A→#00A6A6`) — these are content, not system tokens.

### Note on Theme
Pluto TV ships **dark-only**. There is no light mode; the navy canvas is the brand environment (it makes the blue and yellow read like backlit broadcast graphics). Do not invent a light palette — defer to the OS for any system surface.

## 3. Typography Rules

### Font Family
- **Primary**: `Manrope` (by Mikhail Sharanda / Mirko Velimirović, SIL OFL) — a clean, semi-rounded humanist grotesque that closely matches Pluto's brand sans (geometric, friendly, strong at heavy weights).
- **Alternative**: `Inter` (SIL OFL) — acceptable substitute if Manrope is unavailable; slightly more neutral.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for channel numbers and schedule times so the grid columns align perfectly.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Manrope | 32pt | 800 | 1.1 | -0.5pt | Onboarding / empty-state headlines |
| Screen Title | Manrope | 24pt | 800 | 1.15 | -0.3pt | "Live TV Guide", channel detail title |
| Section | Manrope | 20pt | 700 | 1.2 | -0.2pt | "Featured Channels", On Demand rails |
| Now Playing | Manrope | 17pt | 700 | 1.2 | -0.2pt | Mini-player current program title |
| Body | Manrope | 15pt | 400 | 1.5 | 0pt | Descriptions, synopsis, body copy |
| Channel Name | Manrope | 13pt | 700 | 1.2 | 0pt | Channel name under logo / in lists |
| Program Title | Manrope | 13pt | 600 | 1.2 | 0pt | EPG cell program titles |
| Meta / Schedule | Manrope | 12pt | 500 | 1.3 | 0pt | "9:00 – 10:00 PM · Action" |
| Channel Number | Manrope | 10pt | 800 | 1.0 | 0.2pt | "612" in the channel column (tabular) |
| Time-bar Label | Manrope | 10pt | 700 | 1.0 | 0pt | "9:30 PM" sticky time markers (tabular) |
| Live / On-Now Badge | Manrope | 10pt | 800 | 1.0 | 0.5pt | "LIVE", "ON NOW" — ALL CAPS |
| Tab Label | Manrope | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Heavy and scannable**: channel names and titles are 700–800. The guide must be readable in a glance while channel-surfing.
- **Compact in the grid**: program cells use 12–13pt — the grid prioritizes information density (many channels/programs visible) over generous type.
- **Tabular numerals everywhere numeric**: channel numbers and times use tabular figures so the fixed channel column and time-bar stay perfectly aligned as the grid scrolls.
- **ALL CAPS for status only**: `LIVE`, `ON NOW`, `NEW` are uppercase with positive tracking (`+0.5pt`); program titles and body are sentence case.
- **Cool slate secondary**: metadata is `#9DA8C7` (a cool blue-grey), harmonizing with the navy — never warm grey.
- **Dynamic Type**: titles, body, synopsis scale; EPG cell text, time-bar labels, channel numbers, and badges stay FIXED (the grid is layout-locked).

## 4. Component Stylings

### Buttons

**Primary Button (Watch Live / Watch Now)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#0048FF` (Pluto Blue)
- Text + leading play glyph: `#FFFFFF`, Manrope 15pt weight 800
- Height: 48pt; padding 13pt vertical, 28pt horizontal
- Pressed: background `#0039CC` + scale 0.98

**Highlight Button (Start Watching / Resume — high-emphasis moments)**
- Background: `#FFE100` (Pluto Yellow)
- Text: `#0B0F1F`, Manrope 15pt weight 800
- Corner radius: 8pt; height 48pt
- Pressed: `filter: brightness(0.94)` + scale 0.98
- Use sparingly — yellow signals "the thing that's live/important", not every button

**Secondary Button (+ Watchlist / Share / Set Reminder)**
- Background: transparent
- Border: 1pt `rgba(255,255,255,0.30)`
- Text + icon: `#FFFFFF`, Manrope 14pt weight 700
- Corner radius: 8pt; height 44pt
- Pressed: background `rgba(255,255,255,0.08)`

**Text Button (Full Schedule / See All / More Info)**
- Font: Manrope 14pt weight 700
- Color: `#00C2FF` (Pluto Cyan)
- No background, no underline
- Pressed: opacity 0.7

**Category Pill (All / Movies / News / Sports …)**
- Idle: `#1C2440` bg, `#9DA8C7` text, Manrope 11pt weight 700, 6×12pt padding, 999pt radius
- Active: `#0048FF` bg, `#FFFFFF` text
- Horizontally scrollable strip directly under the guide header

### Core Atoms

**EPG Program Cell (the core unit)**
- Min height: row height (64pt); width = proportional to program duration along the timeline
- Idle: `#141A2E` fill, 1pt `#0B0F1F` right gap-line; title Manrope 13pt weight 600 `#FFFFFF` (truncates with ellipsis), time Manrope 12pt weight 500 `#6B7595`
- **On Now**: `#1C2440` fill + **3pt `#FFE100` left border** + title in full white — the app's signature state
- Future cell: same as idle but time-only label `#6B7595`
- Pressed: brief `#25304F` flash; On Now tap → tune mini-player; future tap → schedule/reminder sheet

**Channel Column Tile (fixed left column)**
- Width: 86pt; row height 64pt; `#141A2E` fill, 0.5pt `#2A3556` right border
- Stacked center: channel number Manrope 10pt weight 800 `#6B7595` (tabular) + channel logo tile (≈40×28pt, 5pt radius, per-channel brand gradient, channel abbreviation in 9pt weight 800 white)
- Sticky horizontally (stays while program timeline scrolls)

**Time-Bar (sticky header)**
- Sits above the grid body, offset by the 86pt channel-column width
- 30-minute increments; each label Manrope 10pt weight 700 `#6B7595` (tabular), min-width matched to the timeline scale
- `#0B0F1F` background, 0.5pt `#2A3556` bottom border, `z-index` above rows, scrolls horizontally in sync with the program timeline

**Mini-Player (pinned top)**
- Height ≈150pt; full-bleed video/gradient
- Top-left `LIVE` chip: `rgba(0,0,0,0.4)` bg, `#FF3B5C` dot + white "LIVE" 10pt weight 800
- Bottom-left: channel line ("CH 612 · PLUTO TV ACTION") in `#00C2FF` 11pt weight 700, program title 18pt weight 800, a thin `#FFE100` progress bar (program elapsed) on a `rgba(255,255,255,0.2)` track
- Tap → expand to full-screen player

**Now-Playing Bar (compact, e.g. when browsing On Demand)**
- `#1C2440` fill, 12pt radius, 12×14pt padding
- 56×38pt thumbnail (6pt radius) + title 13pt weight 700 + "CH 612 · Pluto TV Action" 11pt `#9DA8C7` + trailing `#FF3B5C` dot + "LIVE" 10pt weight 800

### Navigation

**Bottom Tab Bar**
- Height: 54pt + safe-area home indicator
- Background: `rgba(11,15,31,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A3556`
- Tabs (5): Live TV, On Demand, Search, My Pluto, Account
- Icon size: 22pt; label Manrope 10pt weight 600
- Active: icon + label in `#FFE100` (yellow), icon filled
- Inactive: `#6B7595`
- No pill indicator — color + fill change only

**Guide Header**
- Title "Live TV Guide" Manrope 15pt weight 800 + a "Now · 9:41 PM" clock in `#9DA8C7` 11pt weight 700
- Category pill strip directly beneath

**Channel Detail / On Demand Nav**
- Standard top bar: back chevron (`#FFFFFF`, 24pt) + screen title (24pt/800) + optional trailing search/cast

### Input Fields

**Search Bar**
- Height: 44pt; background `#1C2440`; corner radius 8pt
- Leading magnifyingglass 16pt `#6B7595`
- Placeholder "Search channels, shows, movies" 14pt `#6B7595`
- Text `#FFFFFF` 15pt; focus → 1.5pt `#0048FF` border

**Search Results**
- Sectioned: "Channels" (logo + name + channel number rows) and "On Demand" (poster grid, 3-col)

### Distinctive Components

**EPG Guide (the signature surface)**
- Full assembly: pinned mini-player → guide header → category pills → sticky time-bar → scrolling channel rows (fixed channel column + horizontal program timeline). See atoms above. The yellow-left-border "On Now" cell is the defining detail.

**On Now Indicator System**
- Three coordinated signals for "this is airing now": (1) yellow 3pt left border on the EPG cell, (2) `#FF3B5C` dot + "LIVE" in the mini-player/now-playing bar, (3) a thin `#FFE100` elapsed-progress bar in the player. Together they make "what's live" unmistakable.

**Channel Logo Tile**
- A small rounded rectangle with a per-channel brand gradient and an abbreviated channel name — used in the guide's channel column, search results, and channel detail headers.

**Category-Tier Pills**
- The cable-box-style genre tiers (All, Featured, Movies, News, Sports, Comedy, Reality, Kids…) — selecting one filters the visible channel rows; active pill is solid Pluto Blue.

**Schedule / Reminder Sheet**
- Tapping a future program opens a bottom sheet: program art, title, "Airs 10:00 PM · Pluto TV Action", synopsis, and a yellow "Set Reminder" CTA (→ ✓ + `#21D07A` confirm)

**Ad Marker (player)**
- Free linear playback shows ad breaks; the scrubber has `#FFE100` segment ticks at break points; an "Ad · 0:15" chip top-left in `rgba(0,0,0,0.6)`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 16, 20, 24, 32, 40, 48
- Screen horizontal inset: 16pt (for non-grid surfaces)
- Channel-column width: 86pt (fixed)
- EPG row height: 64pt (fixed)
- Program cell internal padding: 8pt vertical, 10pt horizontal
- Time-bar increment: 30 minutes; pixel-width of 30 min defines the timeline scale

### Grid & Container
- The EPG is a **two-axis fixed-grid**: fixed Y (channel rows, vertical scroll) × scrollable X (program timeline) with a frozen left column and frozen top time-bar
- iPhone: channel column 86pt; ~3.5 thirty-minute slots visible at 390pt width
- Non-grid surfaces (On Demand, detail): 16pt insets, conventional rail/poster layout
- iPad: wider channel column (110pt), more time slots visible, mini-player can dock to a side region in landscape
- Search/On Demand grid: 3 columns, 10pt gutter

### Whitespace Philosophy
- **Grid density is the point**: the EPG deliberately packs many channels and programs on screen — it's a TV guide, breadth and scannability beat whitespace.
- **Frozen frame, scrolling content**: the channel column and time-bar never move; only the program timeline and the row list scroll. This stability is what makes the guide feel like a real TV guide.
- **The yellow bar gets clearance**: nothing crowds the On Now cell's left edge — the 3pt yellow border must always be visible at a glance.
- **Non-grid screens breathe more**: On Demand and detail pages use conventional, more generous streaming spacing.

### Border Radius Scale
- Square (0pt): EPG cells, channel-column tiles, time-bar (the grid is rectilinear like a real TV guide)
- Tiny (4pt): progress bars, ad ticks
- Small (5–6pt): channel logo tiles, now-playing thumbnail
- Standard (8pt): buttons, search bar, cards
- Card (10–12pt): On Demand posters, now-playing bar, sheets
- Pill (999pt): category-tier pills, LIVE chips
- Circle (50%): avatars, the live dot

## 6. Depth & Elevation

Pluto conveys elevation through **surface lightening on a flat navy field**, with shadow used sparingly (the EPG itself is intentionally flat and rectilinear, like a broadcast graphic).

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, on `#0B0F1F` | EPG grid background, time-bar, body text |
| Grid Surface | `#141A2E`, no shadow | Idle program cells, channel-column tiles, list rows |
| On Now / Raised | `#1C2440` + 3pt `#FFE100` left border | The On Now cell, now-playing bar, elevated cards |
| Floating (Level 2) | `#25304F`, `0 10px 28px rgba(0,0,0,0.5)` | Dropdowns, context menus, schedule popovers |
| Sheet (Level 3) | `#141A2E`, `0 -10px 36px rgba(0,0,0,0.55)` | Bottom sheets (reminder, channel actions) |
| Modal Overlay | Scrim `rgba(4,6,15,0.7)` | Dim behind modals and sheets |

**Shadow Philosophy**: The guide is a flat, gridded broadcast surface — it uses *brightness and the yellow border*, not drop shadows, to express state and hierarchy (a real TV guide has no shadows). Shadows appear only on truly floating UI (sheets, popovers, expanded player chrome). The mini-player is the one place a soft inner vignette is used for text legibility.

### Motion
- **Channel surf (cell → mini-player)**: tapping an On Now cell cross-fades the mini-player video to the new channel over ~300ms; the tapped cell briefly flashes `#25304F`
- **Time-bar / timeline scroll**: horizontal scroll of the program timeline is 1:1 with the sticky time-bar (they move together, instantly); vertical channel-row scroll is native momentum
- **On Now border pulse (optional)**: the yellow left border can do a very subtle 2s breathing opacity (0.85↔1.0) to draw the eye — keep it minimal
- **Mini-player progress**: the `#FFE100` elapsed bar advances in real time; on channel change it resets and animates to current elapsed over 300ms
- **Category filter**: switching a pill cross-fades the visible rows over 200ms; selected pill fills with Pluto Blue (150ms)
- **Sheet present**: bottom sheet slides up 300ms ease-out; scrim fades to `rgba(4,6,15,0.7)`; dismiss drag 1:1, threshold 40%
- **Set Reminder**: CTA morphs to ✓ with a 250ms spring + brief `#21D07A` flash + soft haptic
- **Player chrome**: tap toggles controls with a 200ms fade; auto-hide after 3s idle
- **Tab switch**: instant content swap; active icon animates color to yellow + fill over 200ms
- **Haptics**: selection tick on channel-cell focus while surfing; soft impact on tune-in and reminder confirm

## 7. Do's and Don'ts

### Do
- Use the deep cool-navy canvas (`#0B0F1F`) — late-night TV glow, NOT pure black
- Make the EPG channel guide the centerpiece — it *is* the app
- Freeze the channel column and time-bar; scroll only the program timeline and row list
- Mark the "On Now" cell with a brighter surface (`#1C2440`) + **3pt `#FFE100` yellow left border** — the #1 signature
- Reserve yellow (`#FFE100`) for live/now emphasis and the active tab — never as a generic accent
- Use Pluto Blue (`#0048FF`) for the wordmark, primary CTA, and active category pill
- Pin a mini-player on top of the guide that streams the selected channel with a LIVE badge
- Use a `#FF3B5C` dot + "LIVE" as the universal live indicator everywhere
- Use tabular numerals for channel numbers and times so the grid stays aligned
- Keep EPG cells rectilinear (square corners) — it's a broadcast grid, not a card stack
- Use cool slate (`#9DA8C7`) for metadata, harmonizing with the navy

### Don't
- Don't add a light mode — Pluto is dark-only; defer to OS for system sheets
- Don't lead with a poster wall — the guide grid is the identity, not Netflix-style rails
- Don't let the channel column or time-bar scroll away — the frozen frame is what makes it a TV guide
- Don't use yellow for ordinary buttons or accents — it must mean "this is on now / live"
- Don't round the EPG cells into cards — keep the rectilinear broadcast-grid look
- Don't drop-shadow the grid — use surface brightness + the yellow border for hierarchy
- Don't use warm grey for metadata — cool slate `#9DA8C7` matches the navy
- Don't use proportional figures for channel numbers/times — columns will misalign
- Don't bury the live indicator — `#FF3B5C` + "LIVE" must always be obvious
- Don't animate the grid aggressively — channel surfing is fast and quiet (200–300ms)
- Don't make the navy pure black — the slight blue lift is what makes the blue/yellow glow

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Channel column 80pt; ~3 thirty-min slots visible; mini-player ~135pt |
| iPhone 13/14/15 | 390pt | Standard: channel column 86pt; ~3.5 slots visible; mini-player ~150pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the mini-player |
| iPhone 15/16 Pro Max | 430pt | ~4 slots visible; mini-player ~165pt; larger program titles |
| iPad (portrait) | 768pt | Channel column 110pt; ~6 slots visible; conventional On Demand grid 4-col |
| iPad (landscape) | 1024pt+ | Mini-player docks to a right rail; guide fills the rest; ~9 slots visible |

### Dynamic Type
- Screen titles, body, synopsis, now-playing title: full scale
- EPG cell text, channel numbers, time-bar labels, LIVE/ON-NOW badges, tab labels: FIXED (the grid is layout-locked; scaling them would break column alignment)
- Schedule-sheet text: scales to L, then truncates

### Orientation
- Phone: guide is portrait-primary; player rotates to landscape fullscreen
- iPad: landscape gets the docked mini-player + wide guide layout
- The frozen channel column / time-bar behavior is preserved in all orientations

### Touch Targets
- EPG program cell: full cell tappable, min 64pt tall
- Channel-column tile: full tile tappable (jumps to that channel)
- Category pill: min 32pt tall, full pill tap
- Tab bar icons: 22pt glyph, 54pt row, full-segment tap
- Mini-player: full surface tap → fullscreen

### Safe Area Handling
- Top: mini-player respects safe area & Dynamic Island; video bleeds under the status bar
- Bottom: tab bar respects home indicator; player controls inset above it
- Sides: non-grid screens use 16pt insets; the EPG timeline bleeds to the right edge
- The sticky time-bar sits below the safe-area / mini-player, never under the notch

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (navy): `#0B0F1F`
- Surface 1 / 2 / 3: `#141A2E` / `#1C2440` / `#25304F`
- Divider (grid lines): `#2A3556`
- Pluto Blue: `#0048FF` (pressed `#0039CC`, bright `#2C6BFF`)
- Pluto Yellow (on-now/live): `#FFE100` / text on it `#0B0F1F`
- Pluto Cyan (links): `#00C2FF`
- Live Red: `#FF3B5C`
- Text primary / secondary / tertiary: `#FFFFFF` / `#9DA8C7` / `#6B7595`
- Success / Error: `#21D07A` / `#FF4A6E`

### Example Component Prompts
- "Build the Pluto TV EPG row in SwiftUI: a 64pt-tall row. Leading: a fixed 86pt channel-column tile — `#141A2E` background, 0.5pt `#2A3556` right border, centered VStack of channel number (Manrope 10pt weight 800 `#6B7595`, tabular) over a 40×28pt logo tile (5pt radius, channel brand gradient, abbreviation in 9pt weight 800 white). Trailing: a horizontally-scrolling row of program cells. Idle cell: `#141A2E`, 1pt `#0B0F1F` right gap-line, program title Manrope 13pt weight 600 white (1 line, ellipsis), time Manrope 12pt weight 500 `#6B7595`, width proportional to duration. The On Now cell: `#1C2440` fill + a 3pt `#FFE100` left border + full-white title."

- "Create the Pluto TV sticky time-bar: a horizontal strip offset 86pt from the left (to clear the channel column), `#0B0F1F` background, 0.5pt `#2A3556` bottom border, `zIndex` above the rows. 30-minute labels in Manrope 10pt weight 700 `#6B7595` (tabular figures), each label min-width matched to the timeline's 30-min pixel scale. It scrolls horizontally in lockstep with the program timeline and stays pinned to the top while channel rows scroll vertically."

- "Build the Pluto TV pinned mini-player: ~150pt tall, full-bleed video with a subtle bottom vignette. Top-left a LIVE chip — `rgba(0,0,0,0.4)` bg, a `#FF3B5C` 7pt dot + 'LIVE' in Manrope 10pt weight 800 white. Bottom-left: channel line 'CH 612 · PLUTO TV ACTION' in `#00C2FF` 11pt weight 700, program title in Manrope 18pt weight 800 white, then a 3pt elapsed-progress bar — `#FFE100` fill on a `rgba(255,255,255,0.2)` track. Tapping the player goes fullscreen."

- "Render the Pluto TV category-tier pill strip: a horizontal scroll of pills directly under the guide header. Idle pill: `#1C2440` background, `#9DA8C7` text, Manrope 11pt weight 700, 6×12pt padding, fully-rounded (999pt). Active pill: `#0048FF` background, white text. Selecting a pill filters the visible channel rows; the fill animates over 150ms."

- "Build the Pluto TV bottom tab bar: 54pt + safe area, `rgba(11,15,31,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A3556`. Five tabs — Live TV, On Demand, Search, My Pluto, Account — 22pt icons, Manrope 10pt weight 600 labels. Active tab icon + label `#FFE100` (yellow) with the icon filled; inactive `#6B7595`. No Material/pill indicator."

- "Create the Pluto TV now-playing bar: `#1C2440` fill, 12pt corner radius, 12×14pt padding, row of: a 56×38pt thumbnail (6pt radius, channel gradient) + a VStack with program title Manrope 13pt weight 700 white and 'CH 612 · Pluto TV Action' Manrope 11pt weight 500 `#9DA8C7` + a trailing group of a `#FF3B5C` 7pt dot and 'LIVE' in Manrope 10pt weight 800 `#FF3B5C`."

### Iteration Guide
1. Canvas is deep cool-navy `#0B0F1F` (slight blue lift) — NOT pure black; dark-only
2. The app *is* the EPG channel guide — a frozen-frame grid, not a poster wall
3. Channel column (86pt) and 30-min time-bar are FROZEN; only the program timeline + row list scroll
4. The "On Now" cell = `#1C2440` surface + **3pt `#FFE100` yellow left border** — the single defining signature
5. Yellow `#FFE100` means "on now / live / important" only — never a generic accent; blue `#0048FF` is the brand CTA
6. A pinned mini-player on top streams the selected channel with a `#FF3B5C` "LIVE" badge
7. EPG cells are rectilinear (square corners) like a broadcast graphic — no card rounding, no shadows on the grid
8. Tabular numerals for channel numbers and times so columns stay aligned while scrolling
9. Metadata is cool slate `#9DA8C7`; titles/channel names are heavy Manrope 700–800
10. Bottom tabs Live TV / On Demand / Search / My Pluto / Account — active icon yellow `#FFE100`, no pill
