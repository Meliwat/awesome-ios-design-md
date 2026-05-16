# Design System Inspiration of Audible (iOS)

## 1. Visual Theme & Atmosphere

Audible's iOS app is a literary listening room rendered in software. The canvas is a warm charcoal (`#1A1A1A`) — not the cold pure-black of music apps, but a deep, paper-at-night gray that frames book covers like a well-lit shelf. Surfaces step up to `#2A2A2A` for cards and the player chrome, with `#3A3A3A` hairline dividers. The mood is calm, premium, and unhurried — this is an app for hours-long sessions, so it avoids visual urgency. Audiobook cover art is the chromatic hero; the UI recedes into warm darkness so the artwork (and the act of reading-by-ear) takes the foreground.

The accent system is a single confident orange. Audible Orange (`#FF9900`) — the Amazon-family amber — is reserved for the primary action: the play affordance, progress fill, the active chapter, and the primary CTA. A pressed orange (`#E68A00`) handles tap-down. There is no second brand hue. White (`#FFFFFF`) is primary text; a soft gray (`#B0B0B0`) carries author names, narrator credits, and metadata. The orange is the verb; everything else is the warm, quiet room around it.

Typography is a deliberate two-family pairing — and this pairing is the brand's editorial signature. Headers use **Playfair Display**, a high-contrast transitional serif that signals *literature* the moment you see it; body, metadata, and controls use **Inter**, a neutral grotesque that stays legible during long sessions (a Voce/serif-headline + sans-body system). Weights stay at 400 / 600 / 700. The most expressive typographic moment is the title screen: the book's title set large in Playfair Display 26-32pt over its cover, with the author in Inter beneath — the look of a hardcover dust jacket.

**Key Characteristics:**
- Warm charcoal canvas (`#1A1A1A`) — a paper-at-night reading room, not cold black
- Audible Orange (`#FF9900`) as the single accent — play, progress, active chapter, primary CTA
- Serif headers (Playfair Display) + sans body (Inter) — the editorial, literary signature
- Audiobook cover hero — large, framed like a hardcover on a shelf
- The player: 30-seconds-back / 30-seconds-forward + a speed dial (0.5×–3.5×)
- Chapter list with the active chapter marked in orange
- Captions toggle (synced text) and a circular progress ring on the cover
- Inter weights 400/600/700; Playfair Display for titles only

## 2. Color Palette & Roles

### Primary
- **Audible Orange** (`#FF9900`): Primary CTA, play button, progress fill, active chapter row, speed-dial active value, "Add to Library".
- **Orange Pressed** (`#E68A00`): Tap-down state for the play button and orange CTAs.
- **Orange Glow** (`rgba(255,153,0,0.28)`): Soft halo under the play button and the progress ring.

### Canvas & Surfaces
- **Canvas Charcoal** (`#1A1A1A`): Primary canvas — the warm reading-room background.
- **Surface 1** (`#2A2A2A`): Cards, the player sheet, list rows, chapter panel.
- **Surface 2** (`#343434`): Pressed rows, elevated panels, the speed-dial sheet.
- **Divider** (`#3A3A3A`): Hairline dividers between chapters and list rows.

### Text
- **Text Primary** (`#FFFFFF`): Book titles, screen headlines, primary UI text.
- **Text Secondary** (`#B0B0B0`): Author names, narrator credits, durations, metadata.
- **Text Tertiary** (`#6E6E6E`): Disabled state, very low-emphasis labels.

### Semantic
- **Progress Orange** (`#FF9900`): The circular progress ring on the cover and the linear chapter progress.
- **Active Chapter** (`#FF9900`): The currently-playing chapter's title + a leading orange bar.
- **Finished Check** (`#FF9900`): Completed-book / finished-chapter checkmark.
- **Captions Active** (`#FF9900`): The Captions toggle when synced text is on.
- **Error Red** (`#E5484D`): Download failures, playback errors.

### Light Mode
Audible's primary listening experience is the warm-dark reading room. A light variant exists for some browse contexts, but the player and library default to charcoal — the warm darkness is core to the long-session, literary identity.

## 3. Typography Rules

### Font Family
- **Headers**: `Playfair Display` (high-contrast transitional serif) — titles, hero, section headers
- **Body / UI**: `Inter` (neutral grotesque) — body, metadata, controls, lists, buttons
- **Production Fallback (headers)**: `'Playfair Display', Georgia, 'Times New Roman', serif`
- **Production Fallback (body)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Tabular figures (Inter) for time remaining and speed values

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Title Screen (Large) | Playfair Display | 30pt | 700 | 1.15 | 0pt | Book title on the player hero |
| Book Title | Playfair Display | 26pt | 700 | 1.2 | 0pt | Title on detail / now-playing |
| Section Header | Playfair Display | 22pt | 700 | 1.2 | 0pt | "Continue Listening", "From Your Wishlist" |
| Card Title (Serif) | Playfair Display | 17pt | 700 | 1.3 | 0pt | Audiobook tile title |
| Author / Subtitle | Inter | 14pt | 400 | 1.3 | 0pt | "By Madeline Miller" |
| Narrator Credit | Inter | 13pt | 600 | 1.3 | 0.2pt | "Narrated by Jake Gyllenhaal" |
| Chapter Title | Inter | 16pt | 600 | 1.3 | 0pt | Chapter row in the chapter list |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Book summary, About the author |
| Captions Text | Inter | 18pt | 400 | 1.6 | 0pt | Synced caption line (large for readability) |
| Meta / Time | Inter | 13pt | 400 | 1.3 | 0pt | "8 hrs 14 min left", tabular |
| Label (UPPER) | Inter | 11pt | 700 | 1.2 | 0.8pt | "AUDIBLE ORIGINAL", section eyebrow |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0.2pt | Orange "Play" / "Add to Library" |
| Button (Secondary) | Inter | 14pt | 600 | 1.0 | 0pt | Outline "Sample" |
| Speed Value | Inter | 15pt | 700 | 1.0 | 0pt | "1.5×" on the speed dial, tabular |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |

### Principles
- **Two families, strict roles**: Playfair Display = headings/titles only; Inter = everything else. Never set body in the serif.
- **Weights 400 / 600 / 700**: regular, semibold, bold — no thin, no black (Playfair's high contrast supplies the drama)
- **No tracking on the serif** (0pt — Playfair's optical spacing is intrinsic); small positive tracking on Inter all-caps labels
- **Tabular numerals on time-remaining and speed values** so digits don't shift mid-session
- **Captions are intentionally large (18pt)** — readability while listening is the priority
- **Dynamic Type respected on titles, authors, body, captions**; speed value and tab labels stay fixed

## 4. Component Stylings

### Buttons

**Primary Play Button (Player)**
- Shape: Circle, diameter 72pt
- Background: `#FF9900`
- Icon: SF Symbol `play.fill` / `pause.fill` in `#1A1A1A` (dark glyph on orange — warm contrast)
- Pressed: scale 0.93, background `#E68A00`, with `.impactOccurred(.medium)` haptic
- Shadow: `rgba(255,153,0,0.28) 0 6px 22px` (orange glow)

**30-Second Skip Buttons (flanking Play)**
- Shape: 44pt circular tap area, no fill (icon-only)
- Icon: SF Symbol `gobackward.30` (back) / `goforward.30` (forward), 30pt, `#FFFFFF`
- The "30" numeral is part of the glyph — these are Audible's signature transport controls
- Pressed: scale 0.9, brief orange tint flash, light haptic

**Primary Pill (Add to Library / Play)**
- Background: `#FF9900`
- Text: `#1A1A1A`
- Padding: 13pt vertical, 28pt horizontal
- Corner radius: 8pt (Audible uses a soft rounded rect, not a full pill)
- Font: Inter 16pt weight 700, +0.2pt tracking
- Pressed: `#E68A00`, scale 0.98

**Outline Pill ("Sample" / "In Library")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `#B0B0B0`
- Padding: 13pt vertical, 24pt horizontal
- Corner radius: 8pt
- Font: Inter 14pt weight 600
- Pressed: border `#FF9900`, text `#FF9900`

**Icon Actions (Bookmark, Sleep, Bookmarks list, Share, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#B0B0B0`
- Active: `#FF9900` (bookmarked, sleep timer running)
- Spacing: evenly distributed in the player's secondary action row

**Text Button ("See all", "Details")**
- Font: Inter 14pt weight 700
- Color: `#B0B0B0`
- No underline, 44pt hit area

### Cards & Containers

**Audiobook Tile (Horizontal shelf)**
- Width: 150pt
- Aspect: 1:1 square cover, 8pt corner radius, subtle `rgba(0,0,0,0.4) 0 6px 16px` shadow (covers feel like physical objects)
- Gap between tiles: 16pt
- Structure: cover → title (Playfair Display 17pt 700, 2-line clamp) → author (Inter 13pt 400 `#B0B0B0`)
- A thin orange progress underline beneath in-progress tiles
- Tap: scale 0.97 with light haptic

**Continue-Listening Row**
- Height: 96pt
- Layout: 72pt × 72pt cover (8pt radius) with an orange circular progress ring around it → stacked title (Playfair 17pt 700) + author (Inter 13pt 400) + "8 hrs left" (Inter 12pt 400 `#B0B0B0`) → trailing 56pt orange play circle
- Background: `#2A2A2A` card, 12pt radius

**Player (Now Playing — Full Screen)**
- Cover art centered, ~280pt × 280pt, 10pt corner radius, with an **orange circular progress ring** tracking around its perimeter (4pt stroke, `#FF9900`, remaining track `#3A3A3A`)
- Below: book title (Playfair Display 30pt 700) + author (Inter 14pt 400 `#B0B0B0`) + narrator (Inter 13pt 600)
- Transport: `gobackward.30` · 72pt orange Play · `goforward.30`
- Below transport: chapter scrubber + "8 hrs 14 min left" (tabular)
- Action row: speed pill · sleep timer · bookmark · chapters · car mode
- Drag up: chapter list sheet

**Chapter List Sheet**
- Bottom sheet, 16pt top corner radius, surface `#2A2A2A`
- Each chapter row: chapter number + title (Inter 16pt 600) + duration (Inter 13pt 400 `#B0B0B0`, tabular)
- The active chapter: title in `#FF9900` with a 3pt orange leading bar; finished chapters show a small orange check

**Speed Dial Sheet**
- Bottom sheet, 16pt top corner radius
- A horizontal arc/slider of speed stops from 0.5× to 3.5× (0.05 increments, snapping to .25 ticks)
- The selected value renders large center (Inter 22pt 700 `#FF9900`, e.g. "1.5×")
- Quick chips: 1.0× · 1.25× · 1.5× · 2.0× as one-tap presets

### Navigation

**Bottom Tab Bar**
- Height: 54pt + safe area
- Background: `rgba(26,26,26,0.96)` with `.regularMaterial` blur
- Tabs: Home, Library, Discover, Profile
- Icon size: 24pt
- Active color: `#FF9900`; inactive: `#B0B0B0`
- Labels: Inter 10pt weight 600, shown always
- Active icon: filled SF Symbol variant; inactive: outlined

**Now Playing Mini Bar**
- Sits directly above the tab bar, full width, 56pt tall
- Background: `#2A2A2A`
- A 3pt orange progress line pinned to the very top edge of the mini bar
- Left: 40pt cover (6pt radius); center: title (Playfair Display 14pt 700, single line) + author (Inter 11pt 400 `#B0B0B0`)
- Right: `gobackward.30` 20pt + `play.fill` / `pause.fill` 22pt
- Tap: expands to the full player with a shared-element transition

**Top Nav (Library / Home Entry)**
- Height: 44pt + safe area
- Title centered (Playfair Display 22pt 700), search + settings top-right
- Filter chips row below on Library (32pt height pills): "All", "Unfinished", "Downloaded"

### Input Fields

**Search Bar**
- Background: `#2A2A2A`
- Height: 44pt
- Corner radius: 10pt
- Leading SF Symbol `magnifyingglass`, 18pt, `#B0B0B0`
- Placeholder: "Search Audible", 16pt 400 `#B0B0B0`
- Focus: 1pt `#FF9900` border, cursor orange
- Text in field: `#FFFFFF`

**Filter Chip**
- Background: `#2A2A2A` default / `#FF9900` selected
- Text: `#FFFFFF` default / `#1A1A1A` selected
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 8pt
- Font: Inter 14pt weight 600
- Examples: "All", "Unfinished", "Downloaded", "Audible Originals"

### Distinctive Components

**The Speed Dial + 30s Skip Player (Signature)**
- Audible's transport is unmistakable: a large orange Play flanked by `gobackward.30` and `goforward.30` (the literal "30" inside the glyph)
- A persistent speed pill (e.g., "1.5×", Inter 15pt 700 `#FF9900`, glass background) sits in the action row; tapping opens the speed-dial sheet
- The speed dial is an arced slider 0.5×–3.5× with large orange numeric feedback and one-tap preset chips
- This skip-30 + variable-speed pairing is the defining audiobook control

**Cover Progress Ring**
- The full-screen player's cover is encircled by a 4pt orange progress ring (remaining = `#3A3A3A`)
- The ring sweeps clockwise as the book progresses — a calm, ambient sense of place in a long work
- The same ring (smaller) wraps the cover on Continue-Listening rows

**Captions (Synced Text)**
- A Captions toggle in the action row; when on, a panel below the cover shows the narrated sentence in Inter 18pt, the current word subtly emphasized, advancing in sync with audio
- Toggle active state is `#FF9900`

**Chapter List with Active Marker**
- The chapter sheet marks the playing chapter with orange title text + a 3pt orange leading bar; finished chapters carry a small orange check — a clear sense of progress through a long structure

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56, 72
- Standard margin: 16pt horizontal, 24pt between shelves
- Player vertical rhythm: 32pt between cover, titles, transport, and action row

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal shelves: 16pt left inset, last tile peeks at the right edge
- Library grid: 2-column cover grid, 16pt gap

### Whitespace Philosophy
- **Unhurried and roomy**: generous vertical spacing — this is a long-session app; nothing feels rushed or dense
- **The cover is framed**: covers carry a real shadow so they read as physical books on a warm shelf
- **The player breathes**: large cover, big transport, comfortable spacing — designed for glanceable control during listening

### Border Radius Scale
- Soft (6-8pt): covers on rows, tiles, the primary pill, search, chips
- Comfortable (10-12pt): the player cover, cards, the continue-listening card
- Sheet (16pt): bottom sheets (chapters, speed dial)
- Circle (50%): the play button, the cover progress ring, avatars, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, canvas |
| Cover (Level 1) | `rgba(0,0,0,0.4) 0 6px 16px` | Audiobook covers feel like physical objects |
| Card (Level 1) | `rgba(0,0,0,0.35) 0 4px 14px` | Continue-listening card |
| Player Cover (Level 2) | `rgba(0,0,0,0.5) 0 14px 36px` | The centered full-screen cover |
| Play Button Glow | `rgba(255,153,0,0.28) 0 6px 22px` | Orange halo under the Play button |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -14px 44px` | Chapter / speed-dial sheets |
| Tab Bar Material | `.regularMaterial` over 96%-opaque charcoal | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Audible's shadows are warm and substantial — book covers cast real, soft shadows so they feel like physical objects resting on a shelf, which reinforces the literary, tactile identity. The expressive accent shadow is the orange glow under the Play button, tying the primary action to the brand. On the warm charcoal canvas these shadows register clearly without feeling harsh; the overall effect is a calm, well-lit reading room rather than a flat screen.

### Motion
- **Play button tap**: scale 0.93 → 1.0 spring (damping 0.72), `.impactOccurred(.medium)` haptic
- **30s skip**: button scale 0.9 + a quick orange tint flash + light haptic; the cover progress ring jumps 30s
- **Speed-dial rotate (signature)**: the speed value scrubs with a rotary feel; each .25 tick gives a light haptic detent, the big number cross-fades
- **Cover progress ring**: continuous clockwise sweep tied to playback position (ambient, no easing)
- **Chapter change**: the active-chapter orange marker slides to the new row over 220ms
- **Mini-bar → Full player**: shared-element 0.32s spring, the cover is the anchor
- **Captions advance**: each line cross-fades in over 180ms; the active word brightens

## 7. Do's and Don'ts

### Do
- Use a warm charcoal canvas (`#1A1A1A`) — a paper-at-night reading room, not cold black
- Reserve Audible Orange (`#FF9900`) for the primary action, progress, active chapter, and CTA
- Pair Playfair Display (titles/headers) with Inter (body/UI) — and keep the roles strict
- Use the `gobackward.30` / `goforward.30` skip controls flanking a 72pt orange Play
- Wrap the player cover in an orange circular progress ring
- Give covers a real, soft shadow so they feel like physical books
- Mark the active chapter in orange with a leading bar; check finished chapters
- Make captions large (18pt Inter) — readability while listening comes first
- Use a speed-dial sheet (0.5×–3.5×) with haptic detents and preset chips

### Don't
- Don't use cold pure-black — Audible's warmth (`#1A1A1A`) is the literary signature
- Don't set body or metadata in the serif — Playfair Display is headings/titles only
- Don't add a second accent color — orange is the entire system
- Don't use generic ±10s skip — the 30-second skip is Audible's signature transport
- Don't use full-pill buttons — Audible buttons are 8pt rounded rects
- Don't flatten the covers — their soft shadow conveys physicality
- Don't use thin or black weights — 400/600/700 only
- Don't make the UI feel urgent or dense — this is an unhurried, long-session app
- Don't animate the progress ring with easing — it sweeps linearly with playback

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 130pt shelf tiles; player cover 240pt |
| iPhone 13/14/15 | 390pt | Standard 150pt tiles; player cover 280pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | 170pt tiles; player cover 320pt; larger captions panel |
| iPad | 768pt+ | 3-column grid; player cover 420pt, chapter list as a side panel |

### Dynamic Type
- Book titles, authors, body, captions, chapter titles: full scale (captions scale generously — readability priority)
- Speed value ("1.5×"): fixed 15pt (dial geometry is exact)
- Time-remaining: scales but stays tabular
- Play button: icon size fixed (32pt within the 72pt circle)
- Tab labels: fixed 10pt

### Orientation
- Home / Library / Discover: **portrait-locked**
- Player: **portrait-locked** (the cover-ring composition is portrait-tuned)
- Car mode: a simplified large-control layout, **landscape-friendly**

### Touch Targets
- Play button: 72pt — unmissable for at-a-glance control
- 30s skip: 44pt circular hit area each
- Speed pill: ≥44pt tall
- Chapter rows: 56pt height, full-row tappable
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: safe area honored on nav (Dynamic Island clears the title)
- Bottom: mini-bar floats above tab bar; both respect the home indicator
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas charcoal: `#1A1A1A`
- Surface 1 (cards/player): `#2A2A2A`
- Surface 2 (pressed/sheets): `#343434`
- Divider: `#3A3A3A`
- Text primary: `#FFFFFF`
- Text secondary: `#B0B0B0`
- Text tertiary: `#6E6E6E`
- Audible Orange (action): `#FF9900`
- Orange pressed: `#E68A00`
- Orange glow: `rgba(255,153,0,0.28)`
- Error red: `#E5484D`

### Example Component Prompts
- "Create the SwiftUI Audible player: warm charcoal #1A1A1A canvas, a 280×280pt centered cover (10pt radius) wrapped in a 4pt orange (#FF9900) circular progress ring (remaining track #3A3A3A). Below: book title in Playfair Display 30pt weight 700 #FFFFFF, author in Inter 14pt weight 400 #B0B0B0, narrator in Inter 13pt weight 600. Transport row: SF Symbol gobackward.30 (30pt #FFFFFF), a 72pt #FF9900 Play circle with play.fill in #1A1A1A and a rgba(255,153,0,0.28) 0 6px 22px glow, goforward.30 (30pt #FFFFFF)."
- "Build the Audible speed-dial sheet: a bottom sheet (#2A2A2A, 16pt top radius) with an arced slider from 0.5× to 3.5× snapping to .25 ticks. The selected value renders large center in Inter 22pt weight 700 #FF9900 (e.g. '1.5×'). One-tap preset chips: 1.0× 1.25× 1.5× 2.0×. Each .25 tick gives a light haptic detent and the big number cross-fades."
- "Design an Audible continue-listening row: 96pt tall #2A2A2A card (12pt radius), a 72×72pt cover (8pt radius) wrapped in an orange circular progress ring, title in Playfair Display 17pt weight 700 #FFFFFF, author in Inter 13pt weight 400 #B0B0B0, '8 hrs left' in Inter 12pt weight 400 #B0B0B0, trailing 56pt orange play circle."
- "Create an Audible chapter list sheet: #2A2A2A surface, rows with chapter number + title in Inter 16pt weight 600 and duration in Inter 13pt weight 400 #B0B0B0 (tabular). The active chapter's title is #FF9900 with a 3pt orange leading bar; finished chapters show a small orange check."
- "Build the Audible Now Playing mini bar: above the tab bar, 56pt tall, #2A2A2A, a 3pt #FF9900 progress line pinned to the top edge. 40×40pt cover (6pt radius), title in Playfair Display 14pt weight 700 (single line) + author in Inter 11pt weight 400 #B0B0B0, trailing gobackward.30 20pt + play.fill 22pt. Tap expands to the full player."

### Iteration Guide
1. Canvas is warm charcoal `#1A1A1A` — a reading room, NOT cold pure-black
2. Audible Orange (`#FF9900`) is the only accent — play, progress, active chapter, CTA
3. Strict type pairing: Playfair Display for titles/headers, Inter for body/UI — never mix roles
4. The signature transport is `gobackward.30` / `goforward.30` flanking a 72pt orange Play
5. Wrap the player cover in an orange circular progress ring; it sweeps linearly with playback
6. Speed dial 0.5×–3.5× with haptic .25 detents and big orange numeric feedback is the defining control
7. Covers cast real soft shadows — they should feel like physical books on a warm shelf
8. Buttons are 8pt rounded rects (not full pills); captions are large 18pt Inter for readability
