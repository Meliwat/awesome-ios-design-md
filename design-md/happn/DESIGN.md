# Design System Inspiration of happn (iOS)

## 1. Visual Theme & Atmosphere

happn's entire product — and therefore its design system — is built around a single, very specific idea: **the people you have crossed paths with in real life**. Not a global swipe deck, not a search radius — happn shows you profiles only after your phone and theirs registered being in roughly the same place at roughly the same time. The signature screen is the **Crossings timeline**: a vertical, scrollable feed where each entry is a location-and-time-stamped card ("Camille, 27 — Le Marais · 11 min ago — You crossed paths 3 times"), strung together on a thin connector line dotted with happn-pink markers. The timeline reads like a diary of near-misses, and the core gesture is sending a **Charm** — a one-tap pink heart that tells someone "I noticed you crossed my path."

The brand color is an unmistakable **happn pink** (`#FF4865`) that on hero moments deepens into a pink→**magenta** (`#E91E63`) gradient. This pink is the connective tissue of the whole UI: it's the timeline dots, the Charm heart, the active tab, the primary button, and the map crossing pins. A warm **rose tint** (`#FF7B93`) softens secondary surfaces, and a single reserved **gold** (`#FFC24B`) means one thing only — **Crush** (mutual instant interest) and premium. The canvas is a cool graphite near-black (`#0E0E12`) in dark mode and a clean off-white in light; happn ships both, but the brand is most recognizable on dark, where the pink and the timeline glow.

The second defining surface is the **Map** — the same crossings rendered as teardrop pins on a city map, so you can literally see *where* your paths intersected (a café, a metro station, a park). Pins use the same pink / gradient / ghost language as the timeline hearts. The third surface is **Chats**, which only unlock after a mutual Charm (a **Crush**), reinforcing that conversation is earned through real-world serendipity. Beyond Charm there's the **FlashNote** — a short opener attached to your Charm so the first message references something real.

Typography is a friendly rounded-geometric: **Poppins** for the wordmark, names, headings, and buttons (its circular bowls feel warm and contemporary, matching happn's optimistic "serendipity" tone), with **Inter** for body copy, location strings, and metadata where dense legibility matters. Weights live in the 400/500/600/700 band. Geometry is soft: cards at 18pt, buttons are full pills (500pt), avatars are 16pt rounded squares, map pins are the classic teardrop. The overall feeling is hopeful, urban, a little romantic, and grounded in *place and time* rather than endless swiping.

**Key Characteristics:**
- Crossings timeline — a vertical connector-line feed of location+time-stamped cards (the signature screen)
- Built on real-world path-crossing, not a global swipe deck or radius search
- happn pink `#FF4865` as connective tissue: dots, Charm heart, active tab, primary CTA, map pins
- Pink→magenta gradient (`#FF4865 → #E91E63`) on hero buttons and the Crush star
- "Charm" — the one-tap pink-heart gesture; "Crush" — mutual Charm that unlocks chat
- Reserved gold `#FFC24B` — Crush / premium only, used nowhere else
- Map view — the same crossings as teardrop pins on a city map (where you crossed)
- FlashNote — a short opener attached to a Charm so first contact references something real
- Location + time stamps everywhere ("Le Marais · 11 min ago", "You crossed paths 3 times")
- Cool graphite canvas `#0E0E12` dark / off-white light — ships both, recognizable on dark
- Poppins (wordmark/names/headings/buttons) + Inter (body/location/meta) — warm rounded-geometric
- Soft geometry — 18pt cards, 500pt pill buttons, 16pt rounded-square avatars, teardrop pins

## 2. Color Palette & Roles

### Primary (Interactive)
- **happn Pink** (`#FF4865`): The brand color and connective tissue — timeline dots, Charm heart, active tab, primary button, map pins, crossing count.
- **Pink Pressed** (`#E5354F`): Pressed state of pink surfaces.
- **Magenta** (`#E91E63`): Gradient end-stop, deep accents — pairs with happn pink in the hero gradient.
- **Hero Gradient**: `linear-gradient(120deg, #FF4865, #E91E63)` — primary hero buttons, Crush star, splash.
- **Rose Tint** (`#FF7B93`): Soft secondary accent, subtle highlights, secondary avatars.
- **Crush Gold** (`#FFC24B`): RESERVED — Crush celebration + premium only. Never decorative.

### Canvas & Surfaces (Dark)
- **Canvas Graphite** (`#0E0E12`): Primary dark canvas — cool near-black, NOT pure black.
- **Surface 1** (`#18181F`): Timeline cards, list rows.
- **Surface 2** (`#21212B`): Elevated chips, ghost buttons, top-bar icons, sheets.
- **Surface 3** (`#2C2C38`): Pressed surface, input fields.
- **Divider** (`#2A2A33`): Hairline dividers AND the timeline connector spine.

### Canvas & Surfaces (Light)
happn also ships a light theme. The brand is most recognizable on dark, but pair these for light.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0E0E12` |
| Surface 1 | `#F6F6F8` | `#18181F` |
| Surface 2 | `#EDEDF1` | `#21212B` |
| Surface 3 | `#E4E4EA` | `#2C2C38` |
| Divider / Spine | `#E6E6EC` | `#2A2A33` |
| Text Primary | `#15151B` | `#F4F4F6` |
| Text Secondary | `#6C6C7A` | `#A0A0AE` |
| happn Pink | `#FF4865` | `#FF4865` |
| Magenta | `#E91E63` | `#E91E63` |
| Crush Gold | `#FFC24B` | `#FFC24B` |

### Text
- **Text Primary** (`#F4F4F6` dark / `#15151B` light): Names, headings, primary body.
- **Text Secondary** (`#A0A0AE` dark / `#6C6C7A` light): Location strings, timestamps, metadata.
- **Text Tertiary** (`#6C6C7A` dark / `#9A9AA6` light): Disabled, very low emphasis, ghost icon strokes.
- **On-Pink** (`#FFFFFF`): Text/icon on pink or gradient fills.
- **On-Gold** (`#1A1A1A`): Text on Crush gold.

### Semantic
- **Success** (`#4ED9A4`): "Charm sent" confirmation, "It's a Crush" success.
- **Error** (`#FF5C5C`): Form errors, block/report confirmation.
- **Warning** (`#FFC24B`): Reuses Crush gold sparingly for caution (rare).
- **Crossing Glow** (`rgba(255,72,101,0.18)`): The 4pt halo ring around active timeline dots.

## 3. Typography Rules

### Font Family
- **Wordmark / Names / Headings / Buttons**: `Poppins` (by Indian Type Foundry & Jonny Pinhorn, SIL OFL) — a geometric sans with near-circular bowls; warm, optimistic, contemporary — matches happn's "serendipity" tone and approximates its rounded brand type.
- **Body / Location / Metadata**: `Inter` (by Rasmus Andersson, SIL OFL) — neutral and dense-legible for location strings, timestamps, chat, and long bios.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- happn's production app uses a custom rounded-geometric; **Poppins** is the closest free, well-hinted match.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Poppins | 32pt | 700 | 1.15 | -0.5pt | Onboarding / empty-state hero |
| Profile Name | Poppins | 26pt | 700 | 1.15 | -0.4pt | Full profile "Camille, 27" |
| Section Title | Poppins | 22pt | 600 | 1.2 | -0.3pt | "People near you" |
| Subsection | Poppins | 18pt | 600 | 1.3 | -0.1pt | "Where you crossed" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Bio text, chat messages |
| Card Name | Poppins | 15pt | 600 | 1.3 | 0pt | Name in timeline card "Léa, 24" |
| Timeline Header | Poppins | 15pt | 600 | 1.3 | 0pt | "Crossed paths today" |
| Location / Meta | Inter | 12pt | 400 | 1.4 | 0pt | "Le Marais · 11 min ago" |
| Crossing Count | Poppins | 12pt | 600 | 1.3 | 0pt | "You crossed paths 3 times" — pink |
| Body Meta | Inter | 14pt | 400 | 1.4 | 0pt | Secondary descriptive lines |
| Button | Poppins | 16pt | 600 | 1.0 | 0pt | Primary / gradient / outline CTA |
| Caption | Inter | 12pt | 400 | 1.3 | 0.1pt | Helper text, fine print |
| Tab Label | Poppins | 10pt | 600 | 1.0 | 0.2pt | Bottom tab labels |

### Principles
- **Two-font system**: Poppins carries warmth/identity (wordmark, names, headings, buttons, crossing count); Inter carries dense legibility (body, location strings, timestamps, chat). Never set body in Poppins.
- **Weights 400/500/600/700 only** — no thin, no black.
- **Crossing count is always pink**: "You crossed paths N times" uses happn pink `#FF4865` — it's the emotional hook of the timeline.
- **Negative tracking on display/section**: tighten -0.3 to -0.5pt at 22pt+ Poppins for a confident wordmark feel.
- **Location strings are quiet**: 12pt Inter `#A0A0AE` with a leading pin glyph — present but never louder than the name.
- **Dynamic Type**: display, name, section, body scale; crossing count, tab labels, location meta stay fixed (layout-sensitive rows).

## 4. Component Stylings

### Buttons

**Primary Button (Send a Charm / Continue)**
- Shape: Full pill, 500pt corner radius
- Background: `#FF4865` (happn pink)
- Text/Icon: `#FFFFFF`
- Padding: 15pt vertical, 30pt horizontal
- Font: Poppins 16pt weight 600
- Pressed: background `#E5354F` + scale 0.98

**Gradient Button (hero "Say hi" / splash CTA)**
- Background: `linear-gradient(120deg, #FF4865, #E91E63)`
- Text: `#FFFFFF`
- Shape: pill 500pt
- Padding: 15pt / 30pt
- Pressed: scale 0.98 + slight darken overlay

**Outline Button (Maybe later / secondary)**
- Background: transparent
- Text: `#F4F4F6` (dark) / `#15151B` (light)
- Border: 1.5pt `#FF4865`
- Shape: pill 500pt
- Padding: 13pt / 24pt
- Pressed: background `rgba(255,72,101,0.10)`

**Text Button (See profile / inline action)**
- Background: none
- Text: `#FF4865`, Poppins 15pt weight 600
- No underline
- Pressed: opacity 0.7

**Icon Button (top-bar search)**
- Size: 17pt glyph in a 34pt circular `#21212B` hit area
- Color: `#F4F4F6`, stroke 2pt
- Pressed: background `#2C2C38`

### Crossings Timeline (the core atom)

The signature happn surface. A vertical scroll where each crossing is a card pinned to a connector line.

**Connector Spine**
- A 2pt vertical line `#2A2A33` running down the left, behind the dots
- Continuous between cards; hidden after the last card

**Crossing Dot**
- 12pt circle, `#FF4865`, with a 4pt halo `rgba(255,72,101,0.18)` ring
- Positioned at the top-left of each card, centered on the spine
- Ghost (older / not-yet-active) variant: dot uses `#2C2C38`, no halo

**Crossing Card**
- Shape: rounded rect, 18pt corner radius
- Background: `#18181F` (dark) / `#F6F6F8` (light), 1pt `#2A2A33` border
- Padding: 12pt; left content inset 26pt to clear the spine/dot
- Layout (horizontal): avatar · meta · Charm heart
  - Avatar: 56pt rounded square, 16pt radius (gradient placeholder)
  - Name: Poppins 15pt w600 `#F4F4F6` ("Camille, 27")
  - Location row: pin glyph 11pt + Inter 12pt `#A0A0AE` ("Le Marais · 11 min ago")
  - Crossing count: Poppins 12pt w600 `#FF4865` ("You crossed paths 3 times")
  - Charm heart (trailing): 40pt circle
- Card-to-card gap: 14pt

**Charm Heart (on card)**
- Active/available: 40pt circle `#FF4865`, white filled heart 18pt, shadow `0 6px 16px -4px rgba(255,72,101,0.5)`
- Ghost (not yet charmed): 40pt circle `#21212B`, 1pt `#2A2A33`, heart outline `#A0A0AE`
- Sent: heart fills + brief scale bounce + success haptic

### Charm · Crush · FlashNote

**Charm** — the core action. A pink heart that signals interest from a crossing.
- Standalone large variant: 56pt circle `#FF4865`, white heart 24pt, shadow `0 8px 20px -6px rgba(255,72,101,0.55)`

**Crush** — a mutual Charm. Unlocks chat. Celebrated with gold.
- Crush star: 56pt circle, hero gradient `linear-gradient(135deg,#FF4865,#E91E63)`, white star 24pt
- Crush celebration screen uses **gold `#FFC24B`** accents (the only place gold appears besides premium)

**FlashNote** — a short opener attached to a Charm.
- Surface: `#21212B`, 1pt `#2A2A33`, 16pt radius, 12/16pt padding
- Text: Inter 13pt `#A0A0AE`, prefixed "FlashNote · "

### Map View

**Crossing Pin (teardrop)**
- Classic teardrop: 44pt body, `border-radius: 50% 50% 50% 6px` rotated 45°, white 30pt inner dot
- Default: `#FF4865`
- Highlighted / mutual: hero gradient `linear-gradient(135deg,#FF4865,#E91E63)`
- Ghost (passed / older): `#21212B`, 1pt `#2A2A33`, `#6C6C7A` inner
- Tap → opens the crossing's profile sheet

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: wordmark "happ**n**" — Poppins 22pt w700, the trailing "n" in `#FF4865`
- Trailing: search glyph 17pt in a 34pt `#21212B` circle
- Background: transparent over canvas

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(14,14,18,0.94)` + `saturate(180%) blur(20px)`, 0.5pt top border `#2A2A33`
- Tabs (4): Timeline · Map · Chats · Profile
- Icon: 23pt; Timeline icon is the happn heart-leaf glyph
- Active: icon filled `#FF4865` + label `#FF4865` Poppins 10pt w600
- Inactive: `#6C6C7A`
- No tint pill — color + fill is the active signal

### Input Fields

**Search Bar**
- Height: 44pt
- Background: `#21212B` (dark) / `#EDEDF1` (light)
- Border: none (focus: 1.5pt `#FF4865`)
- Corner radius: 999pt (pill)
- Leading magnifier 16pt `#A0A0AE`; placeholder Inter 14pt `#A0A0AE`

**Chat Composer**
- Height: 44pt pill, `#21212B`, send button = 34pt pink circle with white arrow
- FlashNote prompt appears above composer on first message

### Distinctive Components

**Crossings Timeline** — see above. The defining happn surface: place + time + frequency of real-world path crossings, on a connector line.

**Charm Heart** — the one-tap pink interest gesture, present on every timeline card and profile.

**Crush Celebration** — the gold moment when a Charm is mutual; chat unlocks.

**FlashNote** — opener text attached to a Charm so first contact references something real.

**Map Crossing Pins** — the same crossings as teardrop pins on a city map; the "where" of serendipity.

**Crossing Count Badge** — "You crossed paths N times" in pink; the emotional hook that differentiates happn from swipe apps.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 18, 20, 24, 26, 32, 40, 56
- Card padding: 12pt; spine clearance inset: 26pt
- Card-to-card gap: 14pt
- Screen side inset: 16pt

### Grid & Container
- iPhone: single timeline column, 16pt side insets
- Map: full-bleed; pins overlay; profile sheet slides up over the map
- iPad: timeline max-width 600pt centered; Map fills, sheet as side panel
- No multi-column content — the timeline is a single focused scroll

### Whitespace Philosophy
- **Timeline rhythm**: 14pt between crossing cards keeps the connector line readable as a continuous thread
- **The spine is sacred**: never let card content cross the 26pt inset that protects the connector line + dots
- **Location is quiet**: generous but secondary; the name and crossing count lead, the place/time supports
- **Map breathes**: pins are spaced so taps don't collide; clusters expand on zoom

### Border Radius Scale
- Square (0pt): full-bleed map
- Medium (8pt): inline tags, small affordances
- Standard (12pt): small cards, sheet sub-elements
- Avatar (16pt): timeline rounded-square avatars; FlashNote surface
- Card (18pt): the signature crossing card
- Comfortable (24pt): bottom-sheet top corners
- Pill (500pt): all buttons, search bar
- Circle (50%): Charm/Crush buttons, dots, tab icons hit area
- Teardrop: map pins (`50% 50% 50% 6px` rotated 45°)

## 6. Depth & Elevation

happn uses soft, pink-tinted elevation — shadows are warm and reserved for the Charm and floating layers.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, connector spine, list text |
| Card (Level 1) | 1pt `#2A2A33` border (dark) / `0 1px 3px rgba(0,0,0,0.06)` (light) | Crossing cards |
| Charm (Level 2) | `0 6px 16px -4px rgba(255,72,101,0.5)` | Charm heart on card |
| Charm Large (Level 2+) | `0 8px 20px -6px rgba(255,72,101,0.55)` | Standalone Charm / Crush button |
| Sheet (Level 3) | `0 -8px 40px rgba(0,0,0,0.5)` | Profile sheet over map, bottom sheets |
| Scrim | `rgba(0,0,0,0.45)` | Dim behind sheets and the Crush takeover |

**Shadow Philosophy**: happn's elevation is *emotional* — the only prominent shadows are the warm pink halos behind the Charm and Crush buttons, which read as "this is the thing to tap." Cards lean on a 1pt border (dark) or a whisper shadow (light) rather than heavy elevation, so the timeline stays calm and the pink pops.

### Motion
- **Charm tap**: heart scales 1.0 → 1.25 → 1.0 over 280ms spring (damping 0.55), fills pink, soft success haptic; on the card the ghost heart morphs to filled
- **Crush reveal**: full-screen gold takeover — two avatars slide together from edges 360ms ease-out, gold sparkle burst, "It's a Crush!" Poppins 32pt, chat-unlock button rises
- **Timeline scroll**: connector spine and dots scroll with content; new crossings fade+slide in from bottom 240ms ease-out as they load
- **Dot pulse**: a freshly-added crossing's dot pulses its halo (scale 1.0 → 1.4 → 1.0) once over 600ms to draw the eye
- **Card press**: scale 0.98 + 80ms ease-out; release springs back
- **Tab switch**: icon fill cross-dissolves to pink 180ms; no pill slide
- **Map pin tap**: pin lifts (translateY -6pt) + bounce 220ms, profile sheet slides up 320ms ease-out
- **Sheet present**: slide up 320ms ease-out; scrim fades in parallel
- **Haptics**: soft impact on Charm sent, success notification on Crush, light selection on tab switch and pin tap

## 7. Do's and Don'ts

### Do
- Make the Crossings timeline the home screen — a connector line of place+time+frequency cards
- Keep happn pink `#FF4865` as the connective tissue — dots, Charm, active tab, primary CTA, pins
- Always show the crossing count in pink ("You crossed paths N times") — it's the emotional hook
- Protect the 26pt spine inset — card content never crosses the connector line
- Use the pink→magenta gradient on hero buttons and the Crush star
- Reserve gold `#FFC24B` strictly for Crush + premium — nowhere else
- Render the Map with the same pink / gradient / ghost teardrop language as the timeline
- Use Poppins for warmth (wordmark, names, headings, buttons) and Inter for legibility (body, location, chat)
- Stamp location + time on every crossing ("Le Marais · 11 min ago")
- Celebrate a Crush with a full gold takeover — conversation is earned through serendipity
- Use a cool graphite canvas `#0E0E12` for dark — not pure black

### Don't
- Don't present a global swipe deck — happn is path-crossings, not a radius search
- Don't let card content cross the connector spine inset (breaks the timeline metaphor)
- Don't use gold for anything but Crush/premium (it dilutes the celebration)
- Don't drop the location/time/frequency stamps — they ARE the product
- Don't set body or location strings in Poppins — Inter carries dense legibility
- Don't tint the whole active tab (pill/label block) — color + icon fill is enough
- Don't make location louder than the name — place supports, name leads
- Don't use pure black for the dark canvas — `#0E0E12` graphite keeps cards readable
- Don't unlock chat before a mutual Charm — a Crush gates conversation by design
- Don't use thin/black font weights — stay 400–700
- Don't replace "Charm"/"Crush" vocabulary with "Like"/"Match" — it changes the brand voice

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatar 48pt; crossing count wraps below location; tighter card padding |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island; top bar + first card start below island |
| iPhone 15/16 Pro Max | 430pt | Avatar 60pt; larger Charm 44pt; name 16pt |
| iPad (portrait) | 768pt | Timeline max-width 600pt centered; Map full with side profile panel |
| iPad (landscape) | 1024pt+ | Split: timeline list (420pt) + Map detail, or Map + profile panel |

### Dynamic Type
- Display, profile name, section, body: full scale
- Crossing count, tab labels, location/time meta: FIXED (layout-sensitive timeline rows)
- Card name scales one step then truncates with ellipsis (never wraps to 2 lines)

### Orientation
- Timeline portrait-preferred on iPhone; Map rotates freely
- iPad supports landscape — list + Map split layout
- Chat rotates freely

### Touch Targets
- Charm heart (card): 40pt circle (≥ 44pt hit via padding)
- Standalone Charm/Crush: 56pt
- Map pin: 44pt teardrop body, 48pt hit
- Tab icon: 23pt glyph, 48pt hit
- Top-bar search: 34pt circle hit
- Crossing card: full-card tap → profile

### Safe Area Handling
- Top: wordmark + first card respect Dynamic Island / notch
- Bottom: tab bar + home indicator respected; last card scrolls clear of tab bar
- Map: pins and sheet respect safe areas; sheet grab handle below notch
- Keyboard (chat): composer rises with keyboard; messages scroll above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0E0E12` dark / `#FFFFFF` light (graphite, not pure black)
- Surface 1 / 2 / 3: `#18181F` / `#21212B` / `#2C2C38`
- Divider / timeline spine: `#2A2A33`
- Text primary / secondary / tertiary: `#F4F4F6` / `#A0A0AE` / `#6C6C7A`
- happn pink: `#FF4865` (pressed `#E5354F`)
- Magenta (gradient end): `#E91E63` — gradient `linear-gradient(120deg,#FF4865,#E91E63)`
- Rose tint: `#FF7B93`
- Crush gold (reserved): `#FFC24B`
- On-pink text: `#FFFFFF`
- Success / Error: `#4ED9A4` / `#FF5C5C`

### Example Component Prompts
- "Build the happn Crossings timeline in SwiftUI: a vertical scroll with a 2pt `#2A2A33` connector spine on the left. Each crossing = a 12pt `#FF4865` dot with a 4pt `rgba(255,72,101,0.18)` halo on the spine, plus a card (18pt radius, `#18181F`, 1pt `#2A2A33` border, 12pt padding, 26pt left inset to clear the spine). Card: 56pt rounded-square avatar (16pt radius), name Poppins 15pt w600 `#F4F4F6` ('Camille, 27'), location row = 11pt pin glyph + Inter 12pt `#A0A0AE` ('Le Marais · 11 min ago'), crossing count Poppins 12pt w600 `#FF4865` ('You crossed paths 3 times'), trailing 40pt Charm heart. 14pt gap between cards."

- "Create the happn Charm heart: 40pt circle `#FF4865` with white filled heart 18pt and shadow `0 6px 16px -4px rgba(255,72,101,0.5)`. Ghost (not yet charmed) variant: 40pt circle `#21212B`, 1pt `#2A2A33`, outline heart `#A0A0AE`. On tap: scale 1.0 → 1.25 → 1.0 over 280ms spring (damping 0.55), fill pink, soft success haptic."

- "Build the happn Crush celebration: full-screen gold takeover. Two circular avatars slide together from left/right edges over 360ms ease-out, a `#FFC24B` gold sparkle burst, 'It's a Crush!' in Poppins 32pt w700 `#F4F4F6`, then a gradient pill button 'Start chatting' (`linear-gradient(120deg,#FF4865,#E91E63)`, Poppins 16pt w600 `#FFFFFF`) rises from the bottom. Success notification haptic."

- "Render a happn map crossing pin: a teardrop — 44pt body, `border-radius: 50% 50% 50% 6px` rotated 45°, 30pt white inner circle. Default `#FF4865`; mutual/highlighted = `linear-gradient(135deg,#FF4865,#E91E63)`; ghost = `#21212B` + 1pt `#2A2A33` + `#6C6C7A` inner. Tap lifts the pin -6pt with a 220ms bounce and slides up the profile sheet 320ms ease-out."

- "Create the happn bottom tab bar: height 64pt + safe area, background `rgba(14,14,18,0.94)` + `saturate(180%) blur(20px)`, 0.5pt top border `#2A2A33`. Four tabs: Timeline (happn heart-leaf glyph), Map, Chats, Profile, icons 23pt. Active = filled icon `#FF4865` + label `#FF4865` Poppins 10pt w600; inactive `#6C6C7A`. No tint pill."

- "Build the happn FlashNote field: surface `#21212B`, 1pt `#2A2A33`, 16pt radius, 12/16pt padding, text Inter 13pt `#A0A0AE` prefixed 'FlashNote · '. It attaches to a Charm so the recipient sees a real opener with the interest."

### Iteration Guide
1. The home screen is the Crossings timeline — a connector-line feed of place+time+frequency cards, NOT a swipe deck
2. happn pink `#FF4865` is the connective tissue — dots, Charm, active tab, primary CTA, map pins
3. The crossing count is always pink ("You crossed paths N times") — it's the emotional differentiator
4. The 26pt spine inset is sacred — card content never crosses the connector line
5. Gold `#FFC24B` is reserved for Crush + premium only — never decorative
6. Hero buttons + the Crush star use the pink→magenta gradient `linear-gradient(120deg,#FF4865,#E91E63)`
7. Type is two-font: Poppins for warmth (wordmark/name/heading/button), Inter for legibility (body/location/chat)
8. Map pins reuse the timeline's pink / gradient / ghost language as teardrops
9. Chat is gated by a mutual Charm (a Crush) — conversation is earned through real-world serendipity
10. Dark canvas is graphite `#0E0E12`, not pure black; cards lift from `#18181F`
