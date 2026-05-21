# Design System Inspiration of Overcast (iOS)

## 1. Visual Theme & Atmosphere

Overcast is the audiophile's podcast app, designed by Marco Arment as a single-developer labor of love, and its iOS interface reflects that ethos: opinionated, restrained, fast, and built around two killer audio features. The most distinctive thing about Overcast's look is its **warm paper-cream light theme** (`#FBFAF6` canvas, `#F7F5EF` surfaces) — it doesn't use stark white; it uses a soft, off-white "paper" that feels like a well-set book. Paired with a single warm **Overcast Orange** (`#FC7E0F`) accent and a true dark theme (`#121212`), the app reads as calm and editorial rather than flashy. There is exactly one accent color and the chrome carries no gradients — the orange is the entire visual identity.

The signature screen is the **episode player (Now Playing)**: a square cover image with a thin orange bottom rule, the episode title and show name, a slim scrubber with an orange knob, and — Overcast's most recognizable control — a **circular play button outlined in orange** (a ring, not a filled disc) flanked by two large skip buttons that show their interval (e.g. "30") underneath a back/forward chevron pair. Below the transport sits the row that defines Overcast: **Smart Speed** and **Voice Boost** toggles. Smart Speed dynamically removes silences from talk audio without changing pitch; Voice Boost applies loudness and EQ tuned for speech. These are not buried in settings — they are first-class, always one tap from the player, and Overcast even surfaces a running "you've saved X hours with Smart Speed" stat as a point of pride.

The second-most-iconic surface is the **Playlists tab**: Overcast organizes listening into smart and manual playlists (All Episodes, In Progress, Starred, plus user-built ones) rendered as tidy rows with a rounded-square colored icon, a name, a one-line description, and an episode count on the trailing edge. The other defining piece is the **recommendation engine** — Overcast lets you publicly recommend episodes and surfaces friends' recommendations, a social layer no other major podcast app has. Overall the personality is "calm, fast, audio-first": one accent, generous list spacing, a paper-warm canvas, the orange ring play button, and Smart Speed/Voice Boost front and center.

**Key Characteristics:**
- Warm paper-cream light theme (`#FBFAF6` canvas, `#F7F5EF` surfaces) — NOT stark white
- True dark theme (`#121212` canvas, `#1C1C1E` surfaces)
- Single Overcast Orange accent (`#FC7E0F`) — identical across both themes
- Circular play button as an orange **ring** (outlined, not filled) — the brand's signature control
- Smart Speed toggle — dynamically removes silences without altering pitch; first-class on the player
- Voice Boost toggle — loudness + EQ tuned for spoken-word clarity; first-class on the player
- "Time saved with Smart Speed" running stat — a signature flex surfaced in the UI
- Playlists tab — smart + manual playlists as tidy rows with colored rounded-square icons
- Recommendation engine — publicly recommend episodes; see friends' recommendations
- Skip buttons that display their interval ("30") beneath a chevron pair
- Restrained chrome — one accent, no gradients, generous list spacing, editorial calm
- 10pt rounded-rectangle buttons; rounded-square (9pt) playlist icons

## 2. Color Palette & Roles

### Primary (Interactive)
- **Overcast Orange** (`#FC7E0F`): The single brand accent — play-button ring, scrubber fill/knob, active Smart Speed / Voice Boost toggles, tab selection, links, primary CTAs. Identical in light and dark.
- **Orange Pressed** (`#E06A00`): Pressed state of orange buttons and the play ring.
- **Link Blue** (`#2D7FF9`): External web links inside show notes (system-blue style); orange is preferred for in-app actions.

### Canvas & Surfaces (Light Mode — paper)
- **Paper Canvas** (`#FBFAF6`): Primary light canvas — warm off-white, NOT pure `#FFFFFF`.
- **Cream Surface** (`#F7F5EF`): Grouped list backgrounds, settings cards, elevated rows.
- **Surface Pressed Light** (`#EDEBE3`): Pressed/selected rows.
- **Divider Light** (`#E4E1D8`): Hairline separators between playlist/episode rows.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas.
- **Dark Surface 1** (`#1C1C1E`): Cards, grouped lists, settings groups.
- **Dark Surface 2** (`#2A2A2C`): Scrubber track, inactive toggle track, pressed rows.
- **Dark Divider** (`#303032`): Hairline separators in dark.

### Text
- **Text Primary Light** (`#1A1A1A`): Episode titles, screen titles on paper.
- **Text Secondary Light** (`#6E6E6E`): Show names, descriptions, metadata on paper.
- **Text Tertiary Light** (`#9A9A9A`): Counts, disabled, low-emphasis.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#9A9A9A`): Secondary text on dark.
- **Text Tertiary Dark** (`#6A6A6A`): Low-emphasis on dark.

### Semantic
- **Success Green** (`#34C759`): Download-complete tick, "Starred" playlist icon, success states.
- **Error Red** (`#FF3B30`): Delete confirmation, failed-download banner.
- **Warning Amber** (`#FF9F0A`): Storage / sync-conflict warnings.
- **Active Orange** (`#FC7E0F`): Reuses the brand accent for "active effect" indication (Smart Speed / Voice Boost ON).

### Playlist Icon Colors (rounded-square tiles)
| Playlist | Icon Tile |
|----------|-----------|
| All Episodes | `#FC7E0F` |
| In Progress | `#2D7FF9` |
| Starred | `#34C759` |
| Downloaded | `#5856D6` |
| Custom (user-pickable) | one of ~10 system tints |

## 3. Typography Rules

### Font Family
- **iOS System**: `SF Pro Text` / `SF Pro Display` (San Francisco) — Overcast uses the system face for native rendering and full Dynamic Type support.
- **Web / Documentation Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest free face for static specs and previews.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for timecodes, durations, and the Smart Speed time-saved stat.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | SF Pro Display | 32pt | 800 | 1.2 | -0.4pt | "Playlists", "Podcasts" large nav title |
| Now Playing Episode | SF Pro Display | 22pt | 700 | 1.25 | -0.3pt | Episode title on player |
| Section Header | SF Pro Display | 22pt | 700 | 1.3 | -0.2pt | "Recommended for You" |
| Episode Title | SF Pro Text | 18pt | 700 | 1.3 | -0.1pt | Episode-detail title |
| List Row Title | SF Pro Text | 15pt | 600 | 1.35 | 0pt | Playlist name, episode-row title |
| Body | SF Pro Text | 16pt | 400 | 1.5 | 0pt | Show notes, descriptions |
| Show Name | SF Pro Text | 14pt | 500 | 1.4 | 0pt | Show name under episode title |
| Metadata | SF Pro Text | 14pt | 400 | 1.4 | 0pt | "1 hr 38 min", "4 new episodes" |
| Playlist Description | SF Pro Text | 12pt | 400 | 1.4 | 0pt | One-line playlist subtitle |
| Stat Caption | SF Pro Text | 12pt | 600 | 1.3 | 0.4pt | "SAVED 1 HR 12 MIN WITH SMART SPEED" uppercase |
| Timecode | SF Mono / SF Pro | 12pt | 500 | 1.0 | 0pt | "48:12", "-1:22:40" tabular |
| Button | SF Pro Text | 16pt | 600 | 1.0 | 0pt | Add Podcast / primary CTA |
| Toggle Label | SF Pro Text | 15pt | 600 | 1.3 | 0pt | "Smart Speed", "Voice Boost" |
| Effect Chip | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Player effect-row labels |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Count | SF Pro Text | 14pt | 600 | 1.0 | 0pt | Trailing episode count on playlist rows |

### Principles
- **Tabular timecodes & stats**: Timecodes, durations, and the Smart Speed time-saved counter use tabular figures so digits don't jitter.
- **One accent only**: Type never uses color for hierarchy except the single orange for links/active state; hierarchy comes from size and weight.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium show names, semibold row titles and toggles, bold episode/section titles, heavy large nav title.
- **Uppercase only for the stat caption**: The "SAVED … WITH SMART SPEED" line is the rare uppercase, tracked caption — a deliberate badge of pride.
- **Dynamic Type first-class**: Titles, body, metadata scale; tab labels, effect chips, timecodes, counts stay fixed for layout stability.

## 4. Component Stylings

### Buttons

**Primary Button (Add Podcast)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: `#FC7E0F` (Overcast Orange)
- Text: `#FFFFFF`, SF Pro Text 16pt weight 600
- Padding: 14pt vertical, 26pt horizontal
- Pressed: background `#E06A00` + scale 0.98

**Outline Button (Recommend)**
- Background: transparent
- Border: 1.5pt `#FC7E0F`
- Text: `#FC7E0F`, SF Pro Text 15pt weight 600
- Corner radius: 10pt
- Pressed: 8% orange fill

**List / Neutral Button (Edit Playlist)**
- Background: `#F7F5EF` light / `#2A2A2C` dark
- Border: 1pt `#E4E1D8` light / `#303032` dark
- Text: `#1A1A1A` light / `#F2F2F2` dark, SF Pro Text 15pt weight 500
- Corner radius: 10pt

**Text Button (See all episodes)**
- Text: `#FC7E0F`, SF Pro Text 15pt weight 600
- No background, no underline
- Pressed: opacity 0.6

**Circular Play Button (signature ring)**
- Size: 66pt diameter
- Style: 2.5pt `#FC7E0F` **ring** (outlined — NOT a filled disc)
- Icon: 24pt orange play triangle / pause bars
- Pressed: ring + icon shift to `#E06A00`, scale 0.96
- This outlined ring is the single most recognizable Overcast control

**Skip Buttons**
- Chevron-pair glyph (27pt) with the interval number ("30") in 9pt w700 `#6E6E6E` directly beneath
- Tap = skip; long-press = continuous seek
- Default intervals user-configurable (commonly back 30 / forward 30 or back 15 / forward 60)

### Cards & Rows

**Playlist Row**
- Height: ~66pt
- Leading: 38pt rounded-square colored icon (9pt corner radius), white glyph
- Name: SF Pro Text 15pt w600 primary
- Description: SF Pro Text 12pt w400 secondary (one line)
- Trailing: episode count, SF Pro Text 14pt w600 secondary
- Divider: 1pt `#E4E1D8` light / `#303032` dark
- Swipe: leading = move to top of Up Next; trailing = settings

**Episode Row (within a playlist)**
- Leading: small circular play/queue button (orange ring when queued)
- Title: SF Pro Text 15pt w600, up to 2 lines
- Meta: SF Pro Text 14pt w400 secondary ("48 min · downloaded · -1:22 left")
- Progress: thin orange underline showing playback position when partially played

**Recommendation Card**
- Friend avatar (28pt circle) + name + "recommended"
- Episode artwork thumbnail (44pt, 8pt corner radius) + episode title
- Optional one-line note from the recommender
- Tap = open episode; orange "Add to playlist" affordance

### Now Playing Player

**Layout (top → bottom)**
1. Top bar: back/collapse chevron (orange, leading), "Now Playing" label (center), list/queue icon (trailing)
2. Cover art: square image, 10pt corner radius, 4–6pt orange bottom rule, soft shadow
3. Episode title (22pt w700) + show name (14pt w500 secondary)
4. Scrubber: 3pt track `#2A2A2C` (dark) / `#E4E1D8` (light), orange fill, 14pt orange circular knob; timecodes below (current / -remaining)
5. Transport row: skip-back (chevron + "30"), ◯ orange-ring play button (66pt), skip-forward (chevron + "30")
6. Effect row: speed (e.g. "1.3×") · Smart Speed · Voice Boost · Star

**Scrubber**
- Track: 3pt height, `#2A2A2C` dark / `#E4E1D8` light, 2pt corner radius
- Fill: `#FC7E0F`, 2pt corner radius
- Knob: 14pt circle `#FC7E0F`, `rgba(0,0,0,0.5) 0 1px 4px` shadow
- Scrubbing: knob grows to 18pt; tooltip shows target timecode

**Effect Chip**
- Vertical icon (19pt stroked) + label (10pt w600) stack
- Inactive: secondary text color
- Active (Smart Speed / Voice Boost ON, non-default speed): `#FC7E0F`
- Speed chip shows the numeric multiplier ("1.3×")

### Smart Speed & Voice Boost (signature)

**Smart Speed Toggle Row**
- Label "Smart Speed" SF Pro Text 15pt w600
- Sub "Dynamically shortens silences in talk shows — no pitch change" 12pt secondary
- Switch: 50×30pt, ON track `#FC7E0F`, OFF track `#2A2A2C`/`#E4E1D8`, 24pt white knob
- When ON, the player shows a small running "saved X with Smart Speed" stat

**Voice Boost Toggle Row**
- Label "Voice Boost", sub "Loudness and EQ tuned for spoken-word clarity"
- Same switch styling

**Speed Control**
- Stepper 0.5× → 3.0× (often shown as a horizontal control in the player sheet)
- Current rate in an orange pill / orange text

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(251,250,246,0.94)` light / `rgba(18,18,18,0.94)` dark with `blur(20px)` + 0.5pt top border
- Tabs (4): Playlists, Podcasts, Search, Settings
- Icon size: 22pt
- Active: `#FC7E0F` (filled glyph), Inactive: tertiary text (stroked glyph)
- No tint pill — color + fill change only
- Labels: SF Pro Text 10pt w600, always shown

**Mini-Player**
- Persistent strip above the tab bar (~58pt): artwork thumb (40pt) + episode title + orange play/pause
- Tap: expands to full player with a 300ms upward sheet slide

**Top Nav (Podcast / Playlist Detail)**
- Large title collapses to compact on scroll
- Leading: back chevron (orange); Trailing: edit / settings (orange)

### Input Fields

**Search Bar (Podcasts / Search tab)**
- Height: 40pt
- Background: `#F7F5EF` light / `#2A2A2C` dark
- Corner radius: 10pt
- Leading `magnifyingglass` 16pt secondary
- Placeholder: "Search podcasts" 15pt secondary
- Focus: 1.5pt `#FC7E0F` border

**Playlist Rule Builder**
- Smart-playlist criteria (podcasts included, episode limit, sort order, downloaded-only)
- Each rule a tappable row opening a sheet
- Color picker for the playlist icon tile (~10 system tints)

### Distinctive Components

**Orange-Ring Play Button**
- The single most recognizable Overcast element: a 66pt circle with a 2.5pt orange stroke and an orange play/pause glyph — outlined, never filled. Used on the player and (smaller) on episode rows.

**Smart Speed Time-Saved Stat**
- A running counter ("You've saved 1 hr 12 min with Smart Speed") shown on the player and in Settings — a deliberate badge of the feature's value.

**Recommendation Engine**
- Publicly recommend an episode; a feed of friends' recommendations with avatars and optional notes. No other major podcast app surfaces this.

**Playlist System**
- Smart playlists (rule-based) + manual playlists; each with a colored rounded-square icon. The Playlists tab is the app's home, not a podcast grid.

**Effect Row on Player**
- Speed · Smart Speed · Voice Boost · Star — always one tap from playback; the row that defines Overcast's audio-first identity.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64
- Playlist/episode row vertical padding: 14pt
- Player horizontal inset: 34pt (cover breathing room)
- Screen content inset: 16pt horizontal

### Grid & Container
- iPhone: single-column lists, full-width rows with 16pt inset, 1pt dividers
- iPad: lists in a sidebar (Playlists/Podcasts/Search/Settings) + detail/player pane
- Player: cover art ~70% screen width, centered
- Mini-player: full-width strip above tab bar

### Whitespace Philosophy
- **Editorial calm**: generous row height and padding; the paper-cream canvas plus a single accent make the UI feel like a well-set page
- **One accent discipline**: orange appears only where action lives (play ring, scrubber, active effects, links, tab selection) — never decoratively
- **Player focus**: cover, title, scrubber, transport, effect row — nothing extraneous

### Border Radius Scale
- Square (0pt): dividers, full-bleed headers
- Subtle (4pt): scrubber track/fill, small badges
- Small (8pt): episode-row artwork thumbnails, recommendation thumbnails
- Standard (9–10pt): playlist icon tiles, buttons, search field, cover art
- Comfortable (14pt): bottom sheets
- Large (20pt): large modal sheets
- Pill (999pt): the speed-rate pill, occasional chips
- Circle (50%): play button ring, avatars, episode-row play

## 6. Depth & Elevation

Overcast is flat and editorial; shadow is reserved for the cover, floating sheets, and the mini-player lift.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, list rows, settings groups |
| Cover (Level 1) | `rgba(0,0,0,0.18) 0 18px 40px -18px` light / `0.7` dark | Player cover art |
| Mini-Player (Level 2) | `rgba(0,0,0,0.12) 0 -2px 12px` | Mini-player strip above tab bar |
| Sheet (Level 3) | `rgba(0,0,0,0.25) 0 -6px 24px` | Bottom sheets (speed, playlist rules, share) |
| Modal Overlay | `rgba(0,0,0,0.4)` | Dim behind sheets/modals |

**Shadow Philosophy**: Overcast doesn't use shadow as decoration — only the player cover gets a soft drop shadow to lift it off the paper. Lists and settings are perfectly flat with hairline dividers. On the paper-cream theme, shadows are gentle (0.12–0.18 opacity) so they don't muddy the warm canvas; on dark they deepen slightly. The single orange accent does the visual work; depth stays subtle.

### Motion
- **Play/Pause**: triangle↔bars morph 200ms ease-in-out; ring + icon scale 0.96 on press
- **Scrubber drag**: knob 14→18pt over 120ms; 1:1 tracking; target-timecode tooltip
- **Smart Speed indicator**: the time-saved stat ticks up live during playback (no animation, just value updates)
- **Mini-player → Full player**: upward sheet slide 300ms ease-out; artwork hero-transitions to the cover
- **Toggle flip (Smart Speed / Voice Boost)**: knob slides 200ms ease-out; track color cross-fades to orange
- **Playlist row swipe**: 1:1 reveal; commit threshold 50%; spring snap 250ms damping 0.85
- **Tab change**: instant icon fill swap to orange; no slide
- **Theme switch (light/dark)**: cross-fade 250ms (no hard cut)
- **Pull-to-refresh (playlist)**: orange spinner; 200ms settle
- **Haptic feedback**: soft impact on play, toggle flip, scrubber boundary, recommend action

## 7. Do's and Don'ts

### Do
- Use the warm paper-cream canvas `#FBFAF6` for light — NOT stark white
- Use a true dark `#121212` canvas for dark mode
- Keep Overcast Orange `#FC7E0F` as the single accent — play ring, scrubber, active effects, links, tabs
- Render the play button as an orange **ring** (2.5pt stroke, outlined) — never a filled disc
- Put Smart Speed and Voice Boost on the player as first-class toggles — never bury them
- Show the "saved X with Smart Speed" stat — it's a deliberate badge of value
- Show skip intervals ("30") beneath the chevron glyph
- Use tabular figures for timecodes, durations, and the time-saved stat
- Organize content into Playlists (smart + manual) with colored rounded-square icons
- Surface the recommendation engine — it's a unique Overcast differentiator
- Keep chrome flat with hairline dividers and generous row spacing

### Don't
- Don't use stark white `#FFFFFF` for the light canvas — `#FBFAF6` paper-cream is the signature
- Don't introduce a second accent — orange is the entire identity
- Don't fill the play button — it must be an outlined orange ring
- Don't add a tint pill behind the active tab — color + fill change only
- Don't bury Smart Speed / Voice Boost in deep settings — they belong on the player
- Don't hide the skip interval — the "30" beneath the chevron is part of the look
- Don't add gradients to chrome — Overcast is flat and editorial
- Don't use proportional figures for timecodes — they shift while scrubbing
- Don't make the app a podcast grid — Playlists is the home surface
- Don't animate aggressively — motion is quiet, 150–300ms ease ranges
- Don't use color for type hierarchy — only size/weight; orange is for action only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cover ~62% width; tighter row padding |
| iPhone 13/14/15 | 390pt | Standard layout; cover ~70% width |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in player top bar |
| iPhone 15/16 Pro Max | 430pt | Larger cover and type; same single-column lists |
| iPad (portrait) | 768pt | Sidebar (Playlists/Podcasts/Search/Settings) + detail; player centered max 520pt |
| iPad (landscape) | 1024pt+ | Persistent sidebar + list + detail/player columns |

### Dynamic Type
- Screen title, episode title, body, metadata: full scale
- Tab labels, effect chips, timecodes, playlist counts: FIXED (layout-sensitive)
- Play ring, scrubber, skip buttons stay fixed-size; only text scales

### Orientation
- Player and lists support rotation
- iPad landscape uses a multi-column split (sidebar + list + detail)
- Cover re-centers on rotation; aspect preserved

### Touch Targets
- Circular play ring: 66pt (well above minimum)
- Skip buttons: 44pt hit (chevron + interval)
- Playlist-row tap: full-row, ≥56pt
- Tab bar icons: 22pt glyph, 44pt hit
- Toggle switches: 50×30pt control, 44pt row hit

### Safe Area Handling
- Top: player top bar respects safe area and Dynamic Island
- Bottom: mini-player + tab bar + home indicator respected
- Mini-player sits directly above the tab bar inside the safe area
- Sides: 16pt list inset; player chrome 34pt

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light, paper): `#FBFAF6`
- Surface (light, cream): `#F7F5EF`
- Canvas (dark): `#121212`
- Surface 1 (dark): `#1C1C1E`
- Surface 2 (dark): `#2A2A2C`
- Divider: `#E4E1D8` light / `#303032` dark
- Overcast Orange: `#FC7E0F`
- Orange pressed: `#E06A00`
- Text primary: `#1A1A1A` light / `#F2F2F2` dark
- Text secondary: `#6E6E6E` light / `#9A9A9A` dark
- Link blue: `#2D7FF9`
- Success green: `#34C759`

### Example Component Prompts
- "Build the Overcast Now Playing screen in SwiftUI: paper-cream `#FBFAF6` (light) / `#121212` (dark) canvas. Top bar — orange back chevron (leading), 'Now Playing' SF Pro 13pt w700 (center), list icon (trailing). Square cover art, 10pt corner radius, with a 5pt `#FC7E0F` bottom rule and a soft `rgba(0,0,0,0.18) 0 18px 40px -18px` shadow. Episode title SF Pro 22pt w700 primary, show name SF Pro 14pt w500 secondary. Scrubber: 3pt track `#E4E1D8`/`#2A2A2C`, fill `#FC7E0F`, 14pt orange circular knob, timecodes below tabular 12pt secondary. Transport: skip-back (chevron + '30' in 9pt w700 secondary), 66pt circular play button as a 2.5pt `#FC7E0F` ring with an orange play triangle, skip-forward (chevron + '30')."

- "Create the Overcast effect row: speed chip ('1.3×'), Smart Speed, Voice Boost, Star — each a vertical 19pt stroked icon + 10pt w600 label. Inactive = secondary text; active (Smart Speed ON, Voice Boost ON, non-default speed) = `#FC7E0F`."

- "Build an Overcast Smart Speed toggle row: label 'Smart Speed' SF Pro 15pt w600 primary, sub 'Dynamically shortens silences in talk shows — no pitch change' SF Pro 12pt secondary. Trailing switch 50×30pt: ON track `#FC7E0F`, OFF track `#2A2A2C` (dark) / `#E4E1D8` (light), 24pt white knob, 200ms slide. 1pt divider below."

- "Render an Overcast playlist row: 66pt tall, leading 38pt rounded-square colored icon (9pt corner radius, white glyph — `#FC7E0F` for All Episodes, `#2D7FF9` In Progress, `#34C759` Starred). Name SF Pro 15pt w600 primary, description SF Pro 12pt w400 secondary (one line). Trailing episode count SF Pro 14pt w600 secondary. 1pt `#E4E1D8`/`#303032` divider."

- "Render the Overcast bottom tab bar: 52pt + safe area, `rgba(251,250,246,0.94)` (light) / `rgba(18,18,18,0.94)` (dark) with 20px blur and 0.5pt top border. 4 tabs — Playlists, Podcasts, Search, Settings. 22pt icons. Active = `#FC7E0F` filled glyph; inactive = tertiary stroked glyph. No tint pill. Labels SF Pro 10pt w600 always shown."

- "Create the Overcast mini-player: persistent strip ~58pt above the tab bar. Leading 40pt artwork thumb (8pt corner radius), episode title SF Pro 14pt w600 primary (1 line) + show name SF Pro 12pt secondary, trailing orange circular play/pause 22pt. Tap expands to full player with a 300ms upward sheet slide; artwork hero-transitions to the cover."

### Iteration Guide
1. Light canvas is warm paper-cream `#FBFAF6` (surfaces `#F7F5EF`) — NEVER stark white
2. Dark canvas is true `#121212` (surfaces `#1C1C1E`)
3. Overcast Orange `#FC7E0F` is the single accent — identical across themes; used only for action
4. The play button is an outlined orange **ring** (2.5pt stroke), never a filled disc — the signature control
5. Smart Speed + Voice Boost are first-class toggles on the player effect row — never buried
6. Surface the "saved X with Smart Speed" stat — it's a deliberate badge of value
7. Skip buttons show their interval ("30") beneath the chevron glyph
8. Playlists (smart + manual) with colored rounded-square icons is the home surface, not a grid
9. Timecodes, durations, and the time-saved stat use tabular figures so digits don't jitter
10. Chrome is flat and editorial — one accent, hairline dividers, generous row spacing, no gradients
