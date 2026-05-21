# Design System Inspiration of Clubhouse (iOS)

## 1. Visual Theme & Atmosphere

Clubhouse's iOS app feels like a warm, well-lit hallway in someone's home rather than a tech product. The canvas is a soft, paper-like cream (`#F2EFE4`) — not white, not gray, a deliberately analog off-cream that reads like recycled stationery. On top of that hallway sits a flow of rounded, hand-drawn-feeling elements: circular profile photos with playful illustrated avatars, fully pill-shaped buttons, and — the defining typographic gesture — room and screen titles set in a **serif display face** (Clubhouse uses a humanist serif; DM Serif Display is the closest free substitute). The combination of cream paper + serif headlines + circular faces gives Clubhouse an intimate, salon, almost literary feel that is the opposite of the cold blue-gray of most social apps.

The product is entirely audio, so the interface is **a stage of faces, not a feed of posts**. The signature screen is a **live Room**: a serif room title at top, then a responsive grid of circular speaker avatars grouped into "Speakers", "Followed by the speakers", and "Others in the room", and a bottom control bar with the famous **"✋ Raise hand"** pill and a quiet **"✌️ Leave quietly"** exit. The single most important visual signal in the entire product is the **emerald speaking ring** (`#38B569`): when someone is talking, their circular avatar gets a 3px emerald ring plus a soft pulsing halo (`rgba(56,181,105,0.18)`). This green pulse is Clubhouse's "now playing" — it is how you read a silent screen and know who has the floor. Hosts get a small 🎙️ pin; moderators a gold accent; muted users dim to ~55% opacity.

The color system is warm and restrained. The hero is cream paper; the one true accent is the Speaking Emerald (used almost exclusively for the speaking ring and the primary "Start a room" CTA). A warm moderator gold (`#E9C46A`) and a soft leave-red (`#E5575C`) are the only other chromatic colors. Avatars carry their own warm gradient palette (peach, sage, lavender, coral, sky) — these provide most of the screen's color, the way photography does for other apps. There is no blue, no cold gray; even the dark mode is a warm near-black (`#1A1A1A`), never a slate.

Typography is a deliberate two-face pairing: a **serif display** for all titles (room titles, screen headers, the "Good morning" greeting, pull-quotes) and a clean **sans (Inter / SF Pro)** for every piece of UI text (names, roles, metadata, buttons, body). The serif is what makes Clubhouse feel like a printed invitation; the sans keeps the controls legible and modern. Nothing is uppercase except tiny overlines; the voice is warm sentence-case throughout.

**Key Characteristics:**
- Cream "hallway" canvas (`#F2EFE4`) light / warm near-black room (`#1A1A1A`) dark — paper, never white/slate
- Serif display titles + sans UI — the signature literary-salon pairing
- Circular illustrated avatars everywhere — the product is faces, not cards
- Emerald speaking ring (`#38B569`) — 3px ring + soft pulse halo = "who has the floor"
- Live Room = grid of speaker circles grouped Speakers / Followed / Others
- "✋ Raise hand" + "✌️ Leave quietly" — emoji-led pill controls with a human voice
- Everything is a pill — buttons, chips, status badges; almost no rectangles
- Host 🎙️ pin + moderator gold + muted 55% dim — role state read at a glance
- The Hallway — a scrollable list of live & upcoming rooms with stacked avatar previews
- Warm gradient avatar palette (peach / sage / lavender / coral / sky) carries the color
- No blue, no cold gray — the whole system is warm; dark mode stays warm

## 2. Color Palette & Roles

### Primary (Interactive)
- **Speaking Emerald** (`#38B569`): The single brand accent — speaking ring/halo, primary "Start a room" CTA, "Open" room status, active toggles.
- **Emerald Deep** (`#2E9A58`): Pressed state of the emerald CTA.
- **Emerald Soft** (`#38B569` @ 18% — `rgba(56,181,105,0.18)`): The pulse-halo behind a speaking avatar, emerald chip backgrounds.

### Canvas & Surfaces (Light — the Hallway)
- **Cream Hallway** (`#F2EFE4`): Primary light canvas — every screen background.
- **Cream Card** (`#FBF9F1`): Slightly lighter card / sheet / row surface lifted off the hallway.
- **Cream Pressed** (`#EAE6D8`): Pressed rows, selected cells.
- **Divider** (`#DDD8C8`): Hairline separators on cream.

### Canvas & Surfaces (Dark — the Room)
- **Dark Room** (`#1A1A1A`): Primary dark canvas — a *warm* near-black, NOT a slate/blue-gray.
- **Dark Surface 1** (`#242220`): Cards, sheets, the room footer — warm-tinted, not neutral.
- **Dark Surface 2** (`#2E2B27`): Soft buttons, pressed rows, mute badges.
- **Dark Divider** (`#3A372F`): Hairline separators on dark.

### Text
- **Ink** (`#1A1A1A`): Primary text on cream (titles, names) — a warm near-black.
- **Ink Secondary** (`#6B6453`): Metadata, roles, captions on cream.
- **Ink Tertiary** (`#9A9483`): Group overlines, disabled on cream.
- **Cream Text Primary** (`#F2EFE4`): Primary text on the dark room.
- **Cream Text Secondary** (`#B8B2A0`): Metadata on dark.
- **Cream Text Tertiary** (`#837D6D`): Overlines, disabled on dark.

### Light + Dark Token Map

| Role | Light (Hallway) | Dark (Room) |
|------|-----------------|-------------|
| Canvas | `#F2EFE4` | `#1A1A1A` |
| Surface 1 | `#FBF9F1` | `#242220` |
| Surface 2 | `#EAE6D8` | `#2E2B27` |
| Divider | `#DDD8C8` | `#3A372F` |
| Text Primary | `#1A1A1A` | `#F2EFE4` |
| Text Secondary | `#6B6453` | `#B8B2A0` |
| Speaking Emerald | `#38B569` | `#38B569` (identical) |
| Moderator Gold | `#E9C46A` | `#E9C46A` (identical) |
| Leave / Error | `#D9534F` | `#E5575C` |

### Avatar Gradient Palette (content color)
Illustrated avatars carry most of the screen's color — assign one per user, deterministically by id.

| Name | Gradient |
|------|----------|
| Peach | `#F4C77B → #E0A24B` |
| Sage | `#9FD8B4 → #5FB484` |
| Lavender | `#C9B6E8 → #9C7FD0` |
| Coral | `#F2A9A0 → #D9776B` |
| Sky | `#A9C7E8 → #6E9CD0` |

### Semantic
- **Speaking** (`#38B569`): The speaking ring + pulse — the most important state in the app.
- **Host / Moderator** (`#E9C46A`): Host 🎙️ pin tint, moderator name accent.
- **Leave / Destructive** (`#E5575C`): "Leave quietly", "End room", remove-from-stage.
- **Muted**: not a color — the speaker's avatar drops to ~55% opacity + a small mute badge.
- **Success** (`#38B569`): "Room scheduled", "Now following" — reuses the emerald.

## 3. Typography Rules

### Font Family
- **Display (Titles)**: Clubhouse uses a humanist serif display for headlines. Closest free substitute: **`DM Serif Display`** (Google Fonts, by Colophon, SIL OFL) — high-contrast, warm, literary.
- **Serif Alternate**: `Lora` (Google Fonts, SIL OFL) — for longer-form serif body if needed (room descriptions in serif).
- **UI / Sans**: `Inter` (by Rasmus Andersson, SIL OFL) — names, roles, metadata, buttons, body. Falls back to SF Pro on iOS.
- **Fallback Stack (serif)**: `'DM Serif Display', Georgia, 'Times New Roman', serif`
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Room Title | DM Serif Display | 28pt | 400 | 1.2 | -0.2pt | The room's topic — the hero of a Room |
| Screen Title | DM Serif Display | 24pt | 400 | 1.2 | -0.2pt | "Good morning, Jia", "Explore" |
| Pull-Quote | DM Serif Display Italic | 20pt | 400 (italic) | 1.3 | 0pt | Onboarding / empty-state lines |
| Section Header | Inter | 18pt | 700 | 1.3 | -0.1pt | "Upcoming for you", "Happening now" |
| Subsection | Inter | 16pt | 700 | 1.3 | 0pt | Card titles in the Hallway list |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Room descriptions, bios |
| Speaker Name | Inter | 15pt | 600 | 1.2 | 0pt | Under each avatar circle |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | All pill buttons |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "218 in the room · 24 speakers" |
| Role Tag | Inter | 12pt | 600 | 1.2 | 0pt | "Host", "Speaker", "Moderator" |
| Overline | Inter | 11pt | 700 | 1.0 | +0.8pt | UPPERCASE group labels ("SPEAKERS") |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0pt | Bottom tab labels |

### Principles
- **Serif for content, sans for chrome**: every title/topic/greeting/quote is the serif display; every name/role/button/meta is Inter. Never set a button or a name in serif; never set a room title in sans.
- **Warm sentence-case voice**: only the tiny group overlines are UPPERCASE. Buttons, titles, and rooms use sentence case ("Raise hand", "Leave quietly", not "RAISE HAND").
- **Weights stay 400 / 600 / 700**: serif at 400 (its high contrast carries the emphasis), sans names at 600, sans headers/buttons at 700. No thin, no black.
- **Emoji are first-class glyphs**: ✋ ✌️ 🎙️ ☕️ are part of the type voice — buttons and room titles routinely lead or end with an emoji. Render them inline at text size.
- **Dynamic Type**: serif titles, section headers, body, names scale; role tags, overlines, tab labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Start a room" / "Join")**
- Shape: Full pill (corner radius = half height, ~26pt on a 52pt button)
- Background: `#38B569` (Speaking Emerald) / text `#07210F` (deep green-black for warmth, AA on emerald)
- Padding: 14pt vertical, 28pt horizontal
- Font: Inter 16pt weight 700
- Often leads with a glyph: "✚ Start a room"
- Pressed: background `#2E9A58` + scale 0.98

**Outline / Secondary Button ("Follow")**
- Background: transparent
- Text: Ink (`#1A1A1A`) / Cream (`#F2EFE4`) on dark
- Border: 1.5pt solid current text color
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: full pill
- Pressed: 8% fill of text color

**Soft Button ("Raise hand ✋")**
- Background: Surface 2 (`#EAE6D8` light / `#2E2B27` dark)
- Text: Ink / Cream, Inter 14–16pt weight 700
- Corner radius: full pill
- Padding: 12pt vertical, 20pt horizontal
- Trailing/leading emoji at text size
- Pressed: surface darkens ~6%

**Leave / Destructive Button ("✌️ Leave quietly")**
- Background: Surface 2 (`#2E2B27`) — NOT a red fill
- Text: Leave Red (`#E5575C`), Inter 14pt weight 700
- Corner radius: full pill
- The voice is gentle — "Leave quietly", never "Exit"

**Icon Button (room top bar — info, share, ⋯)**
- Glyph: 20–22pt stroke, `stroke-width` 2
- Hit area: 44pt
- Color: Text Secondary; pressed → Text Primary
- No background ring

### Speaker Avatar (the core atom)

The defining component. A circular face with state.
- **Shape**: perfect circle, 56–64pt in the Room grid (larger for the active speaker), illustrated/photo fill
- **Idle**: no ring
- **Speaking**: 3pt solid `#38B569` ring + an animated pulse halo `rgba(56,181,105,0.18)` that breathes 1.0 → 1.12 scale at ~0.9s loop while audio is detected
- **Muted**: avatar opacity → ~55% + a 22pt circular mute-badge bottom-right (`#2E2B27` fill, stroke mic-slash, 2pt canvas-colored border)
- **Host**: 🎙️ pin top-right in a 20pt circular badge
- **Moderator**: name rendered in Moderator Gold (`#E9C46A`)
- **Name**: below the circle, Inter 15pt w600, centered, 1-line truncate; optional role line Inter 10–12pt Text Tertiary
- New-speaker entrance: scale 0.8 → 1.0 + fade, 200ms ease-out, soft haptic

### Room Stage (grid of speakers)

- Grouped sections with UPPERCASE overlines: "SPEAKERS", "FOLLOWED BY THE SPEAKERS", "OTHERS IN THE ROOM · {n}"
- Grid: 4 columns on phone (3 on small), 14pt row gap, 6pt column gap
- Speakers section uses the largest circles; "Others" can be smaller (44pt)
- Scrolls vertically; the room title + top bar are sticky, the footer control bar is pinned

### Navigation

**Room Top Bar**
- Height: 52pt + safe area
- Leading: down-chevron (collapse room to a mini-bar) — 18pt stroke `#B8B2A0`
- Trailing: add-people (`person.badge.plus`), share/⋯ — 20pt stroke icons
- Background: canvas, no border (the serif title sits just below it)

**Room Footer / Hand Bar**
- Height: ~72pt + safe area
- `border-top` 0.5pt divider, background = canvas
- Left: "✋ Raise hand" soft pill (becomes "✋ Hand raised" emerald-tinted when active)
- Right: a small speaking-pulse dot + your own mic toggle (mic / mic-slash), 22pt
- When you are a speaker, the left pill becomes a mute/unmute pill instead

**Bottom Tab Bar** (Hallway / Explore / Activity / Profile)
- Height: 52pt + safe area
- Background: canvas with 0.5pt top divider
- 4 tabs: Hallway (house), Explore (compass/search), Activity (bell), Profile (avatar)
- Active: Ink / Cream filled icon + label; inactive: Text Tertiary
- Center "+" optional: a circular emerald FAB-style "Start a room" on the Hallway

**Hallway List Row (a live/upcoming room card)**
- Surface 1 card, 16pt corner radius, 16pt padding, 12pt vertical gap between cards
- Top line: club name + status pill ("● Open" emerald / "Social" / "Closed")
- Title: serif 20pt
- Stacked avatar cluster (3–5 overlapping circles, -10pt overlap) + speaker count
- Trailing: a soft "Join" pill (emerald-tinted if live) or "🔔 reminder" if upcoming

### Input Fields

**Search (Explore / Find people)**
- Height: 44pt
- Background: Surface 1 (`#FBF9F1` / `#242220`)
- Border: none
- Corner radius: full pill
- Leading magnifier 16pt Text Secondary
- Placeholder: "Find people and rooms" Inter 15pt Text Secondary
- Focus: 1.5pt `#38B569` ring

**Room Topic Composer (Start a room sheet)**
- A large serif text input ("What do you want to talk about?") on the canvas, no box, placeholder in Text Tertiary serif
- Audience selector below as three pills: Open / Social / Closed (selected = emerald-tinted)

### Distinctive Components

**Speaking Pulse Ring**
- See Speaker Avatar — the 3pt emerald ring + breathing halo. The single most identifiable Clubhouse element.

**Raise-Hand Bar**
- The "✋ Raise hand" pill at the footer; tapping animates the hand emoji with a small wave (rotate -15° ↔ +15°, 2 cycles) + soft haptic, then the pill flips to "✋ Hand raised" emerald-tinted with a tiny "Waiting" state. Moderators see raised hands as a queue tray that slides up.

**Stacked Avatar Cluster**
- 3–5 circular avatars overlapping by ~10pt (z-ordered left-on-top), used in Hallway cards and "X, Y and 218 others" rows. A trailing "+218" pill chip when overflowing.

**Club Header**
- A club page leads with a serif club name, a circular club icon, member count, and an emerald "Follow"/"Member" pill — same warm card language.

**Ping / Invite Sheet**
- A bottom sheet with a search field + a grid of circular friend avatars; tap to "ping" them into the room (a soft bell animation on the pinged avatar).

**Mini Room Bar**
- When you collapse a room (down-chevron), it docks as a thin pill bar above the tab bar: room title (truncated serif), the active speaker's pulsing circle, and a leave ✕. Tap to re-expand (sheet slides up 300ms).

**Schedule Card**
- An upcoming room: serif title, date/time chip, host avatars, and a "🔔 Set a reminder" pill that toggles to "🔔 Reminder set" emerald-tinted.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 28, 32, 40, 48
- Speaker grid: 14pt row gap, 6pt column gap, 16pt screen inset
- Card padding: 16pt; inter-card gap: 12pt
- Room title: 22pt horizontal inset, 14pt below the top bar

### Grid & Container
- iPhone: speaker grid 4-up (3-up on SE); Hallway cards full-width minus 16pt
- iPad: speaker grid 6–8-up; Hallway cards in a 2-column masonry, max content ~760pt centered
- The room footer + top bar are pinned; only the speaker stage scrolls
- Stacked avatar clusters overlap -10pt and cap at 5 visible + a "+N" pill

### Whitespace Philosophy
- **Faces, not boxes**: the screen is mostly circular avatars on cream — generous gaps so faces breathe; the grid is airy, never dense
- **The serif title gets room**: room/screen titles sit on their own with 14–20pt of clearance, like a printed headline
- **Cards are soft and spaced**: Hallway rows are rounded cards with 12pt between them, never edge-to-edge list rows
- **The emerald is precious**: green appears only for speaking + the primary CTA + open status — never as decoration

### Border Radius Scale
- Square (0pt): essentially unused — Clubhouse avoids hard rectangles
- Soft (8pt): inline chips, small tags
- Standard (12pt): bottom-sheet handles, inputs-as-rect (rare)
- Comfortable (16pt): Hallway room cards, sheets
- Large (24pt): modal sheets, onboarding cards
- Pill (500pt): ALL buttons, status badges, search fields — the default
- Circle (50%): every avatar, the mute badge, the host pin, the speaking ring

## 6. Depth & Elevation

Clubhouse is soft and nearly flat — warmth comes from color and roundness, not heavy shadow.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | The hallway canvas, speaker grid, overlines |
| Card (Level 1) | `rgba(0,0,0,0.05) 0 2px 10px` (light) / none + `#242220` lift (dark) | Hallway room cards |
| Speaking Halo | `0 0 0 8px rgba(56,181,105,0.18)` + breathing scale | The pulse around a speaking avatar (a glow, not a drop shadow) |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -6px 24px`, 24pt top radius | Start-a-room sheet, ping sheet, raised-hands tray |
| Mini Bar (Level 2) | `rgba(0,0,0,0.16) 0 4px 16px` | The collapsed room mini-bar above the tab bar |
| Modal Scrim | `rgba(20,18,16,0.5)` (warm, not pure black) | Behind sheets |

**Shadow Philosophy**: Clubhouse uses minimal, very soft shadows. Hallway cards get a barely-there `0 2px 10px` lift on cream; in dark they rely on the warm `#242220` surface against `#1A1A1A`, not shadow. The one "elevation" that matters is not a shadow at all — it is the **emerald glow halo** around speaking avatars, which is an additive colored glow that breathes. Scrims are warm-tinted (`rgba(20,18,16,0.5)`), never cold black, to preserve the hallway warmth.

### Motion
- **Speaking pulse**: avatar ring + halo breathe scale 1.0 → 1.12 over ~0.9s ease-in-out, looping while voice activity is detected; stops smoothly (200ms) on silence
- **New speaker joins stage**: avatar scales 0.8 → 1.0 + fades in over 200ms ease-out + soft haptic; existing avatars reflow with 200ms ease-out
- **Raise hand**: ✋ emoji waves (rotate ±15°, 2 cycles, 600ms) + soft haptic; pill cross-fades to "Hand raised" emerald-tinted
- **Moderator invites you up**: a celebratory bottom sheet slides up 300ms + medium haptic; your avatar animates from "Others" into the "Speakers" group
- **Room enter**: room sheet slides up full-screen 300ms ease-out; serif title fades + rises 8pt
- **Collapse to mini-bar**: room sheet slides down to a thin docked pill 280ms ease-in; the speaking pulse continues in the mini-bar
- **Leave quietly**: sheet dismisses down 250ms, no confirmation drama — the voice is calm
- **Tab switch**: cross-fade 200ms
- **Pull-to-refresh Hallway**: a custom warm spinner; rooms re-stack with 200ms ease-out
- **Haptics**: soft impact on raise-hand, new-speaker, ping; medium on invited-to-speak; selection tick on audience-pill change
- **Reduce Motion**: the speaking pulse becomes a static 3pt ring (no breathing); sheet transitions become cross-fades; hand-wave is skipped

## 7. Do's and Don'ts

### Do
- Use the cream hallway (`#F2EFE4`) as the light canvas — warm paper, never white
- Keep dark mode warm (`#1A1A1A` / `#242220`) — a warm near-black, never a slate/blue-gray
- Set every title, topic, and greeting in the serif display; every name/role/button in Inter
- Make the emerald speaking ring (`#38B569`, 3pt + soft pulse halo) the primary signal of "who's talking"
- Render every button, status badge, and search field as a full pill
- Make every avatar a perfect circle; use the warm gradient palette for illustrated avatars
- Dim muted speakers to ~55% opacity + a circular mute badge
- Lead controls with emoji where it adds warmth ("✋ Raise hand", "✌️ Leave quietly")
- Group the stage into Speakers / Followed by speakers / Others with UPPERCASE overlines
- Use a warm scrim (`rgba(20,18,16,0.5)`) behind sheets, not cold black
- Keep the voice gentle and human in all copy ("Leave quietly", not "Exit room")

### Don't
- Don't use white or cold gray as the canvas — it must be warm cream / warm near-black
- Don't set room titles or greetings in a sans face — the serif IS the brand
- Don't use rectangles for buttons — everything is a pill
- Don't introduce blue or a cold accent — the only accent is Speaking Emerald
- Don't show "who's talking" with a color fill or a waveform bar — it is the emerald ring + pulse
- Don't make the speaker grid dense — faces need air; it is a salon, not a contact sheet
- Don't use square avatars or rounded-rect cards for people — people are always circles
- Don't make "Leave" a red destructive alarm — the exit is calm and quiet
- Don't animate the speaking pulse aggressively — it is a slow ~0.9s breath, not a strobe
- Don't drop heavy drop-shadows — depth is soft color + roundness, plus the emerald glow
- Don't uppercase buttons or titles — only the tiny group overlines are caps

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Speaker grid 3-up; smaller 52pt avatars; Hallway cards full-bleed |
| iPhone 13/14/15 | 390pt | Standard layout; 4-up speaker grid, 60pt avatars |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the room top bar |
| iPhone 15/16 Pro Max | 430pt | Larger 64pt avatars; serif title can go 30pt |
| iPad (portrait) | 768pt | Speaker grid 6-up; Hallway 2-column masonry; content max ~760pt centered |
| iPad (landscape) | 1024pt+ | Speaker grid 8-up; room footer becomes a centered rail; Hallway 2–3 columns |

### Dynamic Type
- Serif titles, section headers, body, speaker names: full scale
- Role tags, group overlines, tab labels, the "+N" cluster chip: FIXED (layout-critical)
- Speaker names truncate to 1 line; the role line hides at the largest accessibility sizes

### Orientation
- Room supports rotation; the speaker grid reflows column count; footer stays pinned
- Mini room-bar persists across orientation
- Hallway list reflows to a wider card / multi-column on landscape iPad

### Touch Targets
- Speaker avatar: 56–64pt circle, the whole circle + name is the tap target (opens profile card)
- Raise-hand pill: ≥44pt tall
- Mic toggle: 22pt glyph, 44pt hit
- Top-bar icons: 20pt glyph, 44pt hit
- Tab bar: 24pt glyph, 44pt hit
- Hallway card: full card is tappable to enter the room

### Safe Area Handling
- Top: room top bar + serif title respect safe area + Dynamic Island
- Bottom: room footer / hand bar + tab bar respect the home indicator
- Sheets (start-a-room, ping, raised-hands) respect bottom safe area
- Mini room-bar docks just above the tab bar within the safe area

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light hallway): `#F2EFE4`
- Canvas (dark room): `#1A1A1A` (warm near-black, NOT slate)
- Surface 1: `#FBF9F1` light / `#242220` dark
- Surface 2: `#EAE6D8` light / `#2E2B27` dark
- Divider: `#DDD8C8` light / `#3A372F` dark
- Text primary: `#1A1A1A` light / `#F2EFE4` dark
- Text secondary: `#6B6453` light / `#B8B2A0` dark
- Speaking Emerald (the accent): `#38B569`
- Emerald deep (pressed): `#2E9A58`
- Emerald soft (pulse halo): `rgba(56,181,105,0.18)`
- Moderator gold: `#E9C46A`
- Leave / error: `#E5575C`
- Avatar gradients: Peach `#F4C77B→#E0A24B`, Sage `#9FD8B4→#5FB484`, Lavender `#C9B6E8→#9C7FD0`, Coral `#F2A9A0→#D9776B`, Sky `#A9C7E8→#6E9CD0`

### Example Component Prompts
- "Build the Clubhouse live Room screen in SwiftUI on a warm dark canvas (`#1A1A1A`). Top bar 52pt: leading down-chevron (collapse, 18pt `#B8B2A0`), trailing add-people + share icons. Below: a small club overline ('Design Mornings · Open', Inter 12pt `#B8B2A0` with a 6pt `#38B569` dot) and the room title in DM Serif Display 28pt `#F2EFE4`. Then a scrolling stage: an UPPERCASE overline 'SPEAKERS' (Inter 11pt 700 `+0.8` `#837D6D`) and a 4-column grid of 60pt circular avatars, 14pt row gap. A speaking avatar has a 3pt `#38B569` ring + a breathing halo `rgba(56,181,105,0.18)`; the host has a 🎙️ pin badge; a muted speaker is 55% opacity with a mute badge. Footer (72pt, `border-top` 0.5pt `#3A372F`): a soft pill '✋ Raise hand' (`#2E2B27` bg) on the left, a small emerald pulse dot + your mic toggle on the right."

- "Create the Clubhouse speaker avatar component: a perfect circle (60pt) filled with the user's warm gradient (e.g. Sage `#9FD8B4 → #5FB484`) and their initials in Inter 20pt 700 `#2A2620`. States: idle = no ring; speaking = a 3pt solid `#38B569` ring plus an animated halo `rgba(56,181,105,0.18)` breathing scale 1.0 → 1.12 over 0.9s ease-in-out on loop; muted = avatar opacity 55% + a 22pt circular mute badge bottom-right (`#2E2B27` fill, mic-slash stroke, 2pt canvas-colored border); host = a 🎙️ pin in a 20pt badge top-right. Below the circle: the name in Inter 15pt 600 centered, 1-line truncate, with an optional role line in Inter 11pt `#837D6D`."

- "Build the Clubhouse Hallway room card: a `#FBF9F1` card (16pt corner radius, 16pt padding, `0 2px 10px rgba(0,0,0,0.05)`, 12pt gap between cards) on the cream canvas. Top row: club name (Inter 14pt 600 `#6B6453`) + a status pill ('● Open' in `rgba(56,181,105,0.18)` / `#38B569`, full pill). Title: DM Serif Display 20pt `#1A1A1A`. Then a stacked avatar cluster — 4 circular 32pt avatars overlapping -10pt, left-on-top — plus '218 in the room' Inter 14pt `#6B6453`. Trailing: a soft 'Join' pill (emerald-tinted if live)."

- "Create the Clubhouse 'Raise hand' bar: a full-pill soft button (`#2E2B27` bg, Inter 16pt 700 `#F2EFE4`, 12pt/20pt padding) reading '✋ Raise hand'. On tap, the ✋ emoji waves (rotate ±15°, 2 cycles, 600ms) with a soft haptic, then the pill cross-fades to '✋ Hand raised' with an emerald-tinted background (`rgba(56,181,105,0.18)` / text `#38B569`) and a subtle 'Waiting' sublabel."

- "Build the Clubhouse primary CTA: a full-pill button, `#38B569` background, text `#07210F`, Inter 16pt weight 700, 14pt/28pt padding, leading '✚' glyph reading '✚ Start a room'. Pressed: background `#2E9A58` + scale 0.98. Place it as a floating pill on the Hallway."

- "Render the Clubhouse collapsed mini room-bar: a thin docked pill above the tab bar (`#242220`, 16pt radius, `0 4px 16px rgba(0,0,0,0.16)`) showing the room title truncated in DM Serif Display 15pt `#F2EFE4`, the current speaker's 28pt circular avatar with its live `#38B569` pulse still breathing, and a trailing leave ✕ in `#E5575C`. Tap re-expands the room sheet (slide up 300ms ease-out)."

### Iteration Guide
1. Canvas is warm cream `#F2EFE4` (light) / warm near-black `#1A1A1A` (dark) — paper, never white or slate
2. Titles/topics/greetings are DM Serif Display; names/roles/buttons/meta are Inter — never mix the registers
3. The Speaking Emerald `#38B569` is the only accent — a 3pt ring + breathing `rgba(56,181,105,0.18)` halo = "who's talking"
4. Every button, badge, status pill, and search field is a FULL pill; every avatar is a perfect circle
5. The Room is a grid of speaker circles grouped Speakers / Followed by speakers / Others — faces, not a feed
6. Muted = 55% opacity + circular mute badge; host = 🎙️ pin; moderator = gold name — read state at a glance
7. Avatar warm gradients (peach/sage/lavender/coral/sky) carry the screen's color, like photography elsewhere
8. The voice is gentle and emoji-led — "✋ Raise hand", "✌️ Leave quietly", never "EXIT"
9. Depth is soft color + roundness + the emerald glow halo — not heavy drop-shadows; scrims are warm
10. Motion is calm — the speaking pulse is a slow ~0.9s breath, sheets slide 300ms, leaving is undramatic
