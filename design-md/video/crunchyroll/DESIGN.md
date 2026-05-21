# Design System Inspiration of Crunchyroll (iOS)

## 1. Visual Theme & Atmosphere

Crunchyroll's iOS app is a cinema for anime. The entire experience is built on a true-black OLED canvas (`#000000`) so that full-bleed key-art — the painted, saturated, character-forward poster of each show — can bleed edge-to-edge and dissolve into a perfect black floor. Open any title and the first thing you see is a ~60%-of-screen hero image of the show's art, overlaid at the bottom with a black gradient scrim that the title, rating, and genre tags sit on. The philosophy is "the art is the interface": Crunchyroll's catalog is its product, and the UI is engineered to recede so the illustration carries the screen. Chrome is sparse — a translucent status bar floating over the art, a single bright-orange call-to-action, a thin segmented control, and a five-tab bottom bar.

The one color that ever competes with the artwork is **Crunchyroll Orange** (`#F47521`) — the brand's signature "hime" orange. It is used with discipline: the primary "Start Watching" button, the active bottom-tab icon and label, the sliding underline on the segmented control, and the episode-progress bar. Nothing else is orange. This single-accent restraint is deliberate — it means the orange always reads as "the thing to tap" or "where you left off", never as decoration. The second brand color, **Premium Gold** (`#FFC107`), is reserved exclusively for the "Premium" badge and the lock glyph on premium-gated episodes; it never appears as a button or accent.

The signature interaction moment is the **anime detail screen**: a full-bleed key-art hero with a Simulcast/Premium badge row, the show title in heavy Lato 900, a star-rating + year + maturity + genre meta line, a full-width orange "Start Watching E1" (or "Resume E7" if the user has progress) button, a four-icon action row (Watchlist, Rate, Share, Comments), a segmented control (Episodes / Details / More Like This), and then the **resume-aware episode list** — each row a 112×64 thumbnail with a number chip, title, two-line synopsis, and an orange progress bar pinned to the bottom of the thumbnail showing exactly how far the user got. The Simulcast badge (a green chip that means "airing in Japan and here within an hour") and the Sub | Dub control are the two most Crunchyroll-specific affordances.

Typography is **Lato** — a humanist sans with a friendly warmth and a notably heavy 900 "Black" weight that Crunchyroll leans on hard for titles and CTAs. The hierarchy is poster-like: huge heavy titles, medium-weight section headers, light body. Weights concentrate at 400 (body/meta), 700 (titles, episode names), and 900 (hero titles, screen titles, button labels, badges). The black weight is the brand's voice — it makes the app feel bold and energetic, matching anime's intensity. On iOS, Lato falls back to SF Pro when not bundled.

**Key Characteristics:**
- True-black OLED canvas (`#000000`) — no light mode; the app is dark-only so key-art bleeds to a perfect black floor
- Full-bleed key-art hero (~60% of screen) with a bottom-to-black gradient scrim carrying title + meta
- Single accent — Crunchyroll Orange (`#F47521`) for CTA, active tab, segmented underline, progress bar; nothing else
- Premium Gold (`#FFC107`) reserved only for the Premium badge and premium-lock glyph
- Resume-aware episode list — every episode row carries an orange progress bar showing watch position
- Simulcast badge (green `#2BB673`) — Crunchyroll's "airing now in Japan" signature affordance
- Sub | Dub segmented control — switch audio/subtitle track, a core anime-streaming gesture
- Lato Black (900) titles — heavy, energetic poster typography matching anime's intensity
- Segmented control with a sliding orange underline (Episodes / Details / More Like This)
- Five-tab bottom bar: Home / Browse / Watchlist / Manga / Profile
- Content cards are pure key-art posters with minimal chrome — the illustration is the card

## 2. Color Palette & Roles

### Primary (Interactive)
- **Crunchyroll Orange** (`#F47521`): The single brand accent — primary "Start Watching" button, active tab icon + label, segmented-control underline, episode-progress bar, focus rings.
- **Orange Pressed** (`#D8610F`): Pressed/active state of the orange CTA.
- **Orange Tint** (`#FF8C42`): Lighter orange used sparingly for hover/highlight on dark surfaces and inline links.

### Canvas & Surfaces (Dark — the only mode)
- **OLED Canvas** (`#000000`): Primary canvas — true black, lets key-art bleed and saves OLED power.
- **Surface 1** (`#16171A`): Bottom sheets, modals, the "more options" tray.
- **Surface 2** (`#23252B`): Cards on solid backgrounds, list-row press state, ghost-button fill, search field.
- **Surface 3** (`#2E3035`): Inset progress-track background, segmented-control inactive fill, chips.
- **Divider** (`#2A2C31`): Hairline dividers between episode rows and between sections.

> Crunchyroll ships **no light mode**. There is no light token table — the app is dark-only by design so the OLED black floor and key-art are always preserved.

### Text
- **Text Primary** (`#FFFFFF`): Titles, episode names, button labels — pure white on the black canvas.
- **Text Secondary** (`#A0A0A0`): Synopsis, meta lines (year · rating · genre), inactive tab labels.
- **Text Tertiary** (`#6A6C70`): Disabled actions, very low-emphasis captions, timestamps.
- **Text On-Scrim** (`#D8D8D8`): The rating/meta line that sits on the hero gradient scrim (slightly dimmed white for legibility over art).

### Brand & Status Accents
| Role | Color | Usage |
|------|-------|-------|
| Crunchyroll Orange | `#F47521` | Primary CTA, active tab, progress bar, segmented underline |
| Orange Pressed | `#D8610F` | CTA pressed state |
| Premium Gold | `#FFC107` | "Premium" badge fill, premium-lock glyph (text on it: `#1A1304`) |
| Simulcast Green | `#2BB673` | "Simulcast" badge fill |
| New Blue | `#2A9DF4` | "New Episode" badge fill |
| Subtitled Tag | `#FFFFFF` on transparent + `1px rgba(255,255,255,0.5)` border | "Sub \| Dub" outline chip |

### Semantic
- **Error Red** (`#E03E3E`): Playback-error toast, download-failed banner, destructive confirm.
- **Success Green** (`#2BB673`): "Added to Watchlist" toast (reuses Simulcast green).
- **Warning Amber** (`#FFC107`): Offline / data-saver warnings (reuses Premium gold).
- **Scrim Gradient**: `linear-gradient(180deg, transparent 0%, rgba(0,0,0,0.55) 55%, #000000 100%)` — the hero-to-black fade behind title + meta.

## 3. Typography Rules

### Font Family
- **Primary**: `Lato` (by Łukasz Dziedzic, 2010, SIL OFL) — humanist sans with semi-rounded details and a notably heavy 900 "Black" weight; warm, friendly, energetic — the Crunchyroll product face.
- **Heavy Display**: `Lato` weight **900 (Black)** — used for hero titles, screen titles, button labels, and badges; the brand's primary expressive voice.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: When Lato is not bundled, falls back to SF Pro; map Lato Black → SF Pro `.heavy` / `.black` weight.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Lato | 32pt | 900 | 1.1 | -0.5pt | "Browse", "Watchlist" large nav titles |
| Hero Title | Lato | 28pt | 900 | 1.08 | -0.5pt | Show title over the key-art scrim |
| Section Header | Lato | 22pt | 900 | 1.15 | -0.2pt | "Continue Watching", "Popular This Season" |
| Row / Carousel Header | Lato | 18pt | 700 | 1.2 | -0.1pt | Home-screen carousel titles |
| Body | Lato | 16pt | 400 | 1.5 | 0pt | Synopsis paragraph, About text |
| Episode Title | Lato | 15pt | 700 | 1.3 | 0pt | "E1 — The Blade Awakens" |
| Episode Synopsis | Lato | 13pt | 400 | 1.35 | 0pt | Two-line clamp under episode title |
| Meta Line | Lato | 14pt | 400 | 1.4 | 0pt | "2024 · TV-14 · Action, Fantasy" |
| Button Label | Lato | 16pt | 900 | 1.0 | 0.3pt | "Start Watching", "Resume E7" |
| Segmented Item | Lato | 14pt | 900 | 1.0 | 0pt | Episodes / Details / More Like This |
| Badge | Lato | 11pt | 900 | 1.0 | 0.4pt | SIMULCAST / PREMIUM / NEW EPISODE (uppercase) |
| Tab Label | Lato | 10pt | 700 | 1.0 | 0.1pt | Bottom-tab labels |
| Caption / Timestamp | Lato | 12pt | 400 | 1.3 | 0pt | "Aired Oct 12", "24 min" |

### Principles
- **Black weight is the voice**: Hero titles, screen titles, button labels, and badges are all Lato 900 — the heaviness IS the brand. Don't substitute 700 for these.
- **Poster rhythm, not document rhythm**: Type follows entertainment-poster conventions (huge title → small meta), not editorial conventions.
- **Uppercase only for badges and the segmented control** — never for titles or body.
- **Body and synopsis stay 400** — only titles, episode names, and CTAs get weight.
- **Meta lines are secondary gray** (`#A0A0A0`) — year/rating/genre never compete with the title.
- **Dynamic Type**: titles, body, synopsis scale; badges, tab labels, segmented items, progress-bar geometry stay FIXED.

## 4. Component Stylings

### Buttons

**Primary Button ("Start Watching" / "Resume E7")**
- Shape: Rounded rectangle, 8pt corner radius
- Size: full content width, 52pt tall
- Background: `#F47521` (Crunchyroll Orange)
- Text: `#FFFFFF`, Lato 16pt weight 900, 0.3pt tracking
- Leading icon: filled play triangle, 18pt, `#FFFFFF`, 10pt gap before label
- Pressed: background `#D8610F` + scale 0.98
- Disabled: background `#23252B`, text `#6A6C70`

**Secondary / Outline Button ("Add to Watchlist")**
- Background: transparent
- Border: 1.5pt `rgba(255,255,255,0.4)`
- Text: `#FFFFFF`, Lato 14pt weight 700
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Pressed: background `rgba(255,255,255,0.08)`

**Ghost Button ("Resume" on cards)**
- Background: `#23252B`
- Text: `#FFFFFF`, Lato 14pt weight 700
- Corner radius: 8pt
- Padding: 12pt vertical, 20pt horizontal

**Text Button ("See all episodes")**
- Background: none
- Text: `#F47521`, Lato 14pt weight 900
- No underline
- Pressed: opacity 0.6

**Icon Action (Watchlist / Rate / Share / Comments row)**
- Vertical stack: 22pt stroked glyph + 11pt weight-700 label
- Color: `#A0A0A0` default; the Watchlist glyph turns `#F47521` filled when the title is saved
- 8pt vertical gap between glyph and label; evenly distributed in the action row

### Content Card (the core atom)

Crunchyroll's catalog is rendered as key-art poster cards. The card *is* the illustration.

**Poster Card (Browse grid / carousel)**
- Aspect ratio: 2:3 (vertical poster) in grids; 16:9 (landscape thumbnail) in "Continue Watching"
- Corner radius: 6pt
- Image: full-bleed show key-art, full color, no overlay by default
- Optional top-left badge: Simulcast / New Episode chip, 8pt inset
- Optional bottom progress bar: 3pt orange fill on `rgba(255,255,255,0.25)` track (Continue Watching only)
- Title below card: Lato 14pt weight 700 `#FFFFFF`, 1-line truncate, 8pt top gap
- Pressed: scale 0.97, 150ms ease-out

**Hero Card (detail-screen top)**
- Height: ~60% of screen (≈420pt on a 390pt-wide device)
- Full-bleed key-art, no corner radius (bleeds to screen edges)
- Bottom scrim: `linear-gradient(180deg, transparent, rgba(0,0,0,0.55) 55%, #000 100%)`
- Overlaid content (badges, title, meta) anchored 18pt from bottom

### Episode Row (signature component)

- Container: full width, 12pt vertical padding, 1pt `#2A2C31` bottom divider
- Thumbnail: 112×64pt, 6pt corner radius, full-color still
- Episode-number chip: top-left of thumbnail, `rgba(0,0,0,0.7)` bg, `#FFFFFF` Lato 10pt weight 900, 2×6pt padding, 3pt radius — e.g. "E1"
- Progress bar: pinned to thumbnail bottom, 3pt tall, `rgba(255,255,255,0.25)` track + `#F47521` fill at watched fraction
- Title: Lato 15pt weight 700 `#FFFFFF`
- Synopsis: Lato 13pt weight 400 `#A0A0A0`, 2-line clamp, 3pt top gap
- 12pt gap between thumbnail and text column

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area (≈64pt total)
- Background: `rgba(0,0,0,0.95)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2A2C31`
- Tabs (5): Home, Browse, Watchlist, Manga, Profile
- Icon size: 22pt
- Active: `#F47521` (filled glyph + label)
- Inactive: `#6A6C70` (stroked glyph + label)
- Labels: Lato 10pt weight 700, always shown
- No tint pill — active state is pure orange color change

**Top Bar (detail screen)**
- No solid bar — a translucent back chevron + share/cast icons float directly over the key-art at the safe-area inset
- On scroll, a solid `#000000` bar with the compact show title fades in once the hero title scrolls out

**Top Bar (Home / Browse)**
- Height: 44pt + safe area
- Background: transparent over content, `#000000` when scrolled
- Leading: Crunchyroll wordmark (Home) or screen title (Browse)
- Trailing: search glyph + notifications bell, 22pt, `#FFFFFF`

### Input Fields

**Search Field**
- Height: 44pt
- Background: `#23252B`
- Corner radius: 8pt
- Leading icon: magnifier 16pt `#A0A0A0`
- Placeholder: "Search anime, manga…" Lato 15pt weight 400 `#6A6C70`
- Text: `#FFFFFF`
- Focus: 1.5pt `#F47521` border

**Segmented Control (Episodes / Details / More Like This)**
- A row of text items separated by 24pt, sitting above a 1pt `#2A2C31` divider
- Item: Lato 14pt weight 900 — `#A0A0A0` inactive, `#FFFFFF` active
- Active indicator: 3pt `#F47521` bar pinned to the divider beneath the active item, 2pt radius
- Tap or swipe to switch; the orange bar slides 220ms ease-out to the new item

### Distinctive Components

**Content Badge**
- Pill-less rounded rect, 4pt corner radius, Lato 11pt weight 900 uppercase, 0.4pt tracking, 4–5pt padding
- Variants: **Simulcast** (`#2BB673` bg / `#FFF` text), **Premium** (`#FFC107` bg / `#1A1304` text), **New Episode** (`#2A9DF4` bg / `#FFF` text)
- Placement: top-left of poster cards (8pt inset) or in a badge row above the hero title

**Sub | Dub Control**
- An outline chip or two-segment toggle: "SUB" and "DUB" labels, Lato 12pt weight 900
- Inactive segment: transparent + 1pt `rgba(255,255,255,0.5)` border, text `#FFFFFF`
- Active segment: `#F47521` fill, text `#FFFFFF`
- 6pt corner radius; lives in the episode header or player overlay

**Episode Progress Bar**
- 3pt (in-card) to 4pt (standalone) tall, fully rounded
- Track: `rgba(255,255,255,0.25)` (over art) or `#2E3035` (on solid)
- Fill: `#F47521` at the exact watched fraction; 0% = no visible fill
- The single most repeated Crunchyroll affordance — every resumable episode carries it

**Hero Scrim**
- A bottom-anchored gradient (`transparent → rgba(0,0,0,0.55) → #000`) over the key-art so white title + gray meta stay legible regardless of the underlying illustration
- Never a solid panel — always a gradient that resolves to the true-black canvas

**Premium Lock**
- A `#FFC107` lock glyph (14pt) overlaid on premium-gated episode thumbnails, paired with the gold "Premium" badge — the only places gold appears

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64
- Screen horizontal inset: 20pt (content), 0pt (full-bleed hero + carousels)
- Episode-row vertical padding: 12pt
- Section vertical gap: 24pt

### Grid & Container
- iPhone: 20pt horizontal content insets; hero + carousels are full-bleed
- Browse grid: 3-column poster grid (2:3 aspect), 8pt gutters, ~12pt edge inset
- Carousel: horizontally scrolling row, 8pt card gap, first card flush to 20pt inset
- iPad: 5–6 column grid; detail screen content max-width 760pt centered, hero stays full-bleed

### Whitespace Philosophy
- **Art-forward**: the key-art hero is the dominant element; chrome is intentionally sparse so illustration breathes
- **Black is the spacing**: the true-black canvas IS the negative space — no card borders or separators on the home grid
- **Tight episode rhythm**: 12pt row padding keeps the episode list scannable and dense
- **Single-accent discipline**: orange appears in ≤4 roles per screen — restraint is the system

### Border Radius Scale
- Square (0pt): Full-bleed hero, status bar, dividers
- Subtle (4pt): Badges, episode-number chip
- Card (6pt): Poster cards, thumbnails, Sub|Dub chip
- Standard (8pt): Buttons, search field, ghost buttons
- Comfortable (12pt): Bottom sheets, modals
- Pill (500pt): Progress-bar caps, occasional filter chips
- Circle (50%): Avatars, profile glyph

## 6. Depth & Elevation

Crunchyroll is nearly flat. Depth comes from the key-art and its scrim, not from shadow stacks.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Home grid, episode rows, content on the black canvas |
| Scrim (Level 1) | `linear-gradient(180deg, transparent, rgba(0,0,0,0.55), #000)` | Hero-to-black fade behind title + meta |
| Floating CTA (Level 2) | `0 4px 16px rgba(0,0,0,0.6)` | The orange "Start Watching" button when it floats over art |
| Sheet (Level 3) | `0 -8px 32px rgba(0,0,0,0.7)` | Bottom sheets (download options, language picker, more) |
| Modal Overlay | `rgba(0,0,0,0.6)` | Dim behind modal dialogs and sheets |

**Shadow Philosophy**: On a true-black canvas, drop-shadows are nearly invisible — so Crunchyroll uses the **scrim gradient** as its primary depth cue. The hero art "sits behind" a black fade; the orange CTA "sits above" it with a soft shadow. Sheets slide up from below with a deep upward shadow. Content rows are pure flat — separated only by `#2A2C31` hairlines.

### Motion
- **Hero parallax**: as the detail screen scrolls, the key-art translates at ~0.5× scroll speed (subtle parallax) while the scrim and content scroll at 1×
- **Top-bar reveal**: solid `#000` bar + compact title cross-fade in over 200ms once the hero title scrolls past the safe area
- **Segmented underline**: the orange bar slides to the tapped/swiped segment over 220ms ease-out
- **Card press**: scale 0.97 in 150ms ease-out, release back to 1.0 in 150ms
- **Tab switch**: instant content swap; active-tab color cross-fades to orange over 120ms
- **Sheet present**: bottom sheet slides up 300ms ease-out; backdrop fades to `rgba(0,0,0,0.6)`
- **Play transition**: tapping "Start Watching" fades the detail screen to black over 250ms, then the player fades up — a deliberate "lights down" cinematic beat
- **Progress fill**: when returning to a partially-watched title, the orange progress bar animates from 0 → watched fraction over 400ms ease-out
- **Haptic feedback**: light impact on Watchlist toggle, segment change, and pull-to-refresh trigger; medium impact on "Start Watching"

## 7. Do's and Don'ts

### Do
- Use true black (`#000000`) as the canvas — Crunchyroll is OLED-first and dark-only
- Let key-art bleed full-width and dissolve into the black floor via the scrim gradient
- Use Crunchyroll Orange (`#F47521`) for exactly the CTA, active tab, segmented underline, and progress bar — nothing else
- Reserve Premium Gold (`#FFC107`) only for the Premium badge and premium-lock glyph
- Put an orange progress bar on every resumable episode and Continue-Watching tile
- Use Lato 900 (Black) for hero titles, screen titles, button labels, and badges — the heaviness is the brand
- Use the Simulcast (green) badge for currently-airing-in-Japan titles
- Provide the Sub | Dub control on episodes — anime audiences expect to choose track
- Keep chrome sparse — the illustration is the interface
- Use the scrim gradient (not a solid panel) so title + meta stay legible over any art

### Don't
- Don't add a light mode — Crunchyroll has none; a light canvas breaks the OLED key-art floor
- Don't introduce a second accent color — orange is the only interactive accent
- Don't use gold (`#FFC107`) as a button or general accent — it means "Premium" exclusively
- Don't put borders or cards around poster art — the black canvas is the separator
- Don't substitute weight 700 for hero/screen titles — they must be Lato 900
- Don't use uppercase for titles or body — uppercase is only for badges and the segmented control
- Don't let meta lines (year/rating/genre) use white — they are secondary gray `#A0A0A0`
- Don't use heavy drop-shadows on the black canvas (they're invisible) — use the scrim and hairlines
- Don't animate aggressively — motion is cinematic and calm (150–300ms ease-out), including the "lights down" fade-to-black before playback
- Don't hide the episode-progress bar — resume position is the core re-engagement hook
- Don't render the hero scrim as a solid block — it must be a gradient resolving to `#000000`

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ≈ 380pt tall; 3-col Browse grid; tab labels stay |
| iPhone 13/14/15 | 390pt | Standard layout; hero ≈ 420pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by floating translucent top controls |
| iPhone 15/16 Pro Max | 430pt | Hero ≈ 460pt; 3-col grid with larger posters |
| iPad (portrait) | 768pt | 5-col Browse grid; detail content max-width 760pt centered; hero full-bleed |
| iPad (landscape) | 1024pt+ | 6-col grid; two-pane Browse + detail; player fills screen |

### Dynamic Type
- Scales: hero title, section headers, body, synopsis, meta
- Fixed: badges, tab labels, segmented items, episode-number chip, progress-bar geometry
- Long titles wrap to 2 lines max in the hero; episode synopsis clamps to 2 lines

### Orientation
- Portrait-primary for browsing screens; the video player is landscape-locked full-screen
- iPad supports landscape browsing with a wider grid and optional two-pane detail

### Touch Targets
- Primary CTA: 52pt tall (exceeds 44pt minimum)
- Tab icons: 22pt glyph, 49pt+ hit area
- Episode row: full-row tap, 64pt+ effective height
- Segmented item: 14pt text, 44pt hit height
- Sub | Dub chip: 44pt hit height

### Safe Area Handling
- Top: floating translucent back/share/cast controls respect safe area and the Dynamic Island
- Bottom: bottom tab bar + home indicator respected; player controls inset from the home indicator
- Hero key-art extends *under* the status bar (full-bleed) but title/meta stay below the safe area
- Sides: 20pt content inset; hero and carousels are full-bleed

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (true black, dark-only — no light mode)
- Surface 1 (sheets): `#16171A`
- Surface 2 (cards/press): `#23252B`
- Surface 3 (tracks/chips): `#2E3035`
- Divider: `#2A2C31`
- Text primary: `#FFFFFF`
- Text secondary: `#A0A0A0`
- Text tertiary: `#6A6C70`
- Crunchyroll Orange (the only accent): `#F47521`
- Orange pressed: `#D8610F`
- Premium Gold (badge only): `#FFC107` (text on it `#1A1304`)
- Simulcast Green (badge): `#2BB673`
- New Blue (badge): `#2A9DF4`
- Error: `#E03E3E`
- Hero scrim: `linear-gradient(180deg, transparent, rgba(0,0,0,0.55) 55%, #000 100%)`

### Example Component Prompts
- "Build the Crunchyroll anime detail hero in SwiftUI: a full-bleed key-art image filling ~60% of screen height, no corner radius. Overlay a bottom gradient scrim `linear-gradient(180deg, .clear, Color.black.opacity(0.55) at 55%, .black)`. Anchored 18pt from the bottom: a badge row (green `#2BB673` SIMULCAST chip + gold `#FFC107` PREMIUM chip, each Lato 11pt weight 900 uppercase, 4pt radius, 4×8pt padding), then the show title in Lato 28pt weight 900 `#FFFFFF`, then a meta line `★ 4.9 · 2024 · TV-14 · Action` in Lato 14pt weight 400 `#A0A0A0`."

- "Create the Crunchyroll primary CTA: a full-width 52pt-tall button, 8pt corner radius, background `#F47521`, a 18pt filled white play triangle, 10pt gap, then 'Start Watching E1' in Lato 16pt weight 900 `#FFFFFF` with 0.3pt tracking. Pressed: background `#D8610F` + scale 0.98. If the user has progress, label becomes 'Resume E7'."

- "Render a Crunchyroll episode row: 112×64pt thumbnail (6pt radius, full-color still) on the left. Top-left of the thumbnail: an episode chip 'E1' in `rgba(0,0,0,0.7)` bg, `#FFFFFF` Lato 10pt weight 900, 2×6pt padding, 3pt radius. Pinned to the thumbnail bottom: a 3pt progress bar, track `rgba(255,255,255,0.25)`, fill `#F47521` at the watched fraction. Right column: title in Lato 15pt weight 700 `#FFFFFF`, then a 2-line-clamped synopsis in Lato 13pt weight 400 `#A0A0A0`. 12pt gap, 12pt vertical padding, 1pt `#2A2C31` bottom divider."

- "Build the Crunchyroll segmented control: a row of items 'Episodes', 'Details', 'More Like This' spaced 24pt apart in Lato 14pt weight 900 — active `#FFFFFF`, inactive `#A0A0A0` — sitting above a 1pt `#2A2C31` divider. A 3pt `#F47521` indicator (2pt radius) pins to the divider under the active item and slides 220ms ease-out when a new item is tapped or the content is swiped."

- "Create the Crunchyroll bottom tab bar: 50pt + safe area, background `rgba(0,0,0,0.95)` with blur(20px) saturate(180%), 0.5pt top border `#2A2C31`. Five tabs — Home, Browse, Watchlist, Manga, Profile — 22pt glyphs with Lato 10pt weight 700 labels always shown. Active tab: filled glyph + label in `#F47521`. Inactive: stroked glyph + label in `#6A6C70`. No tint pill."

- "Build a Crunchyroll content badge component with three variants: SIMULCAST (`#2BB673` bg, `#FFF` text), PREMIUM (`#FFC107` bg, `#1A1304` text), NEW EPISODE (`#2A9DF4` bg, `#FFF` text). Each: Lato 11pt weight 900 uppercase, 0.4pt tracking, 4pt corner radius, 4pt vertical / 8pt horizontal padding. Placed top-left of poster cards at an 8pt inset, or in a row above the hero title."

### Iteration Guide
1. Canvas is true black `#000000` — dark-only, no light mode; key-art must bleed to a perfect black floor
2. Crunchyroll Orange `#F47521` is the only accent — CTA, active tab, segmented underline, progress bar; never a second accent
3. Premium Gold `#FFC107` is for the Premium badge / lock only — never a button or general accent
4. Every resumable episode and Continue-Watching tile carries an orange progress bar at its watched fraction
5. Hero titles, screen titles, button labels, badges are Lato 900 (Black) — the heaviness is the brand
6. The hero uses a bottom-to-black gradient scrim (not a solid panel) so title + meta stay legible over any art
7. Simulcast (green) badge = airing now in Japan; the Sub | Dub control = audio/subtitle track switch — both are signature
8. Chrome is sparse — content cards are pure key-art posters; the black canvas is the separator
9. Motion is cinematic and calm (150–300ms ease-out), including a "lights down" fade-to-black before the player appears
10. Meta lines (year/rating/genre) are secondary gray `#A0A0A0` — they never use white or compete with the title
