# Design System Inspiration of Feeld (iOS)

## 1. Visual Theme & Atmosphere

Feeld's iOS app is the dating app for the open-minded — non-monogamy, kink, queer, exploratory — and its design language is deliberately the opposite of the candy-bright, photo-heavy mainstream dating apps. The app is **dark-native**: there is no light mode in the shipping product. The canvas is true black (`#000000`), profile photography is the only large field of color, and the entire interface floats over that black like neon on velvet. The single brand accent is an electric **acid-yellow** (`#E8FF63`) — a color that reads as energetic, queer-coded, and intentionally un-romantic (no soft pinks-as-default here). A warm **desire pink** (`#FF5C8A`) is the secondary emotional color, and a soft **lilac** (`#C9B8FF`) handles the "Wink"/super-interest gesture.

The signature screen is the **Discover stack**: a single, deeply-rounded (28pt) profile card filling most of the screen, with a heavy bottom gradient so the name, age, and the person's **Desires** chips read against any photo. Below the card sits a three-button action row — a circular Pass (outline), a large acid-yellow Like (the only acid fill on the screen), and a lilac Wink/super-like. Feeld supports **couples and groups**, so the card frequently shows two names ("Robin & Sky"), two ages, and shared desires — the design must never assume a single-person profile. Above the card is a minimal top bar with a hamburger (filters) and a settings glyph.

The other defining concept is **Desires**: Feeld lets members tag what they're into (Non-monogamy, Kink, ENM, specific orientations, pronouns) and these appear everywhere as pill chips — selected (acid fill, black text), unselected (dark surface, hairline border), or emotional (pink-tinted). The profile, the filter sheet, and the onboarding all revolve around these chips. The second core surface is **Connections** — Feeld's word for matches/chats — a list of round avatars, names (often paired), and last-message snippets.

Typography is a quirky-humanist geometric: **Space Grotesk** for display, names, and headings (its irregular letterforms feel human and a little subversive, matching the brand), with **Inter** for body and metadata where neutral legibility matters. Weights stay in the 400/500/600/700 band — no hairline, no black. Hierarchy is product-UI (display → name → section → body → meta → chip), not editorial. Everything is rounded: cards at 28pt, buttons are full pills (500pt), avatars are circles. The result reads as confident, adult, inclusive, and a little underground.

**Key Characteristics:**
- True-black canvas (`#000000`) — dark-native, no light mode in the shipping app
- One rationed accent: acid-yellow `#E8FF63` — lands on exactly one primary action per screen
- Desire pink `#FF5C8A` (emotional/secondary) + lilac `#C9B8FF` (Wink/super-interest)
- Discover stack: one full-bleed 28pt-rounded profile card with heavy bottom gradient
- Couples/groups first-class — cards show paired names, paired ages, shared desires
- Three-button action row: outline Pass · acid Like (the only acid fill) · lilac Wink
- "Desires" pill-chip system everywhere — selected / unselected / emotional states
- "Connections" (not "Matches") — list of round avatars + paired names + snippets
- Space Grotesk (display/names) + Inter (body/meta) — quirky-humanist over editorial
- Fully rounded geometry: 28pt cards, 500pt pill buttons, circular avatars
- Verified badge as a small acid-checkmark glass pill on the photo
- Minimal chrome — black bar, hamburger filters, no tab-bar tint pill

## 2. Color Palette & Roles

### Primary (Interactive)
- **Acid Yellow** (`#E8FF63`): The single brand accent — primary Like button, active tab indicator, selected chips, verified checkmark. Rationed to one primary action per screen.
- **Acid Pressed** (`#D4EB4F`): Pressed state of acid surfaces.
- **Desire Pink** (`#FF5C8A`): Secondary emotional action — "Send a Wink"/secondary CTA, pink-tinted chips, liked-you affordances.
- **Pink Deep** (`#E0447A`): Pressed state of pink surfaces.
- **Lilac** (`#C9B8FF`): Wink / super-interest gesture, premium accents.

### Canvas & Surfaces (Dark — the only mode)
- **Canvas Black** (`#000000`): Primary canvas. True black so photography and accent feel like neon.
- **Surface 1** (`#121212`): Cards-on-black, top-bar icons, list rows.
- **Surface 2** (`#1C1C1E`): Elevated chips, action buttons (Pass), bottom sheets.
- **Surface 3** (`#262626`): Pressed/hover surface, input fields.
- **Divider** (`#2A2A2A`): Hairline dividers between connection rows and sections.
- **Scrim** (`rgba(0,0,0,0.72)`): Bottom gradient on profile cards so text reads on any photo.

### Light Mode
Feeld ships **dark-only**. There is no first-class light theme. If a host platform forces a light surface (e.g., a share sheet), keep accents identical (`#E8FF63`, `#FF5C8A`) on white `#FFFFFF` with text `#1A1A1A` — but this is a system surface, not the Feeld UI.

| Role | Dark Mode (shipping) | Forced-Light (system surfaces only) |
|------|----------------------|--------------------------------------|
| Canvas | `#000000` | `#FFFFFF` |
| Surface 1 | `#121212` | `#F4F4F2` |
| Surface 2 | `#1C1C1E` | `#EAEAEA` |
| Divider | `#2A2A2A` | `#E2E2E2` |
| Text Primary | `#FFFFFF` | `#1A1A1A` |
| Text Secondary | `#A8A8A8` | `#6E6E6E` |
| Acid Yellow | `#E8FF63` | `#E8FF63` |
| Desire Pink | `#FF5C8A` | `#FF5C8A` |

### Text
- **Text Primary** (`#FFFFFF`): Names, headings, primary body on black.
- **Text Secondary** (`#A8A8A8`): Metadata ("Couple · 31 & 29 · 2 km away"), message snippets.
- **Text Tertiary** (`#6E6E6E`): Timestamps, disabled, very low emphasis.
- **On-Acid** (`#000000`): Text/icon on acid-yellow fills — always black for contrast.
- **On-Pink** (`#FFFFFF`): Text on pink fills.

### Semantic
- **Success** (`#66E0A3`): Verification success, "Connected" confirmation.
- **Error** (`#FF6B6B`): Form errors, blocked/report confirmation.
- **Warning** (`#E8FF63`): Reuses acid for caution callouts (consistency over a separate amber).
- **Liked-You Glow** (`rgba(232,255,99,0.5)`): Drop-shadow halo behind the acid Like button.

## 3. Typography Rules

### Font Family
- **Display / Names / Headings**: `Space Grotesk` (by Florian Karsten, derived from Colophon's Space Mono, SIL OFL) — a proportional grotesque with irregular, slightly mechanical letterforms that feel human and subversive — on-brand for Feeld's non-mainstream identity.
- **Body / Metadata / UI**: `Inter` (by Rasmus Andersson, SIL OFL) — neutral, highly legible at small sizes for message snippets, metadata, form fields.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- Feeld's production app uses a custom grotesque; **Space Grotesk** is the closest free, well-hinted match for the quirky-geometric character.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Space Grotesk | 32pt | 700 | 1.15 | -0.5pt | Onboarding / empty-state hero |
| Profile Name | Space Grotesk | 26pt | 700 | 1.15 | -0.4pt | On card, supports paired names ("Robin & Sky") |
| Section Title | Space Grotesk | 22pt | 600 | 1.2 | -0.3pt | "Your Desires", "Connections" |
| Subsection | Space Grotesk | 18pt | 600 | 1.3 | -0.1pt | "About this connection" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Bio text, prompt answers |
| List Title | Space Grotesk | 15pt | 600 | 1.3 | 0pt | Connection name in list |
| Card Meta | Inter | 14pt | 400 | 1.4 | 0pt | "Couple · 31 & 29 · 2 km away" |
| Message Snippet | Inter | 13pt | 400 | 1.4 | 0pt | Last message in Connections list |
| Chip | Inter | 12pt | 600 | 1.0 | 0.1pt | Desire / identity pill text |
| Button | Space Grotesk | 16pt | 600 | 1.0 | 0pt | Primary / secondary CTA |
| Caption | Inter | 12pt | 400 | 1.3 | 0.1pt | Timestamps, helper text |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.2pt | (icons-only is also valid; dot indicator) |

### Principles
- **Two-font system**: Space Grotesk carries personality (display, names, sections, buttons); Inter carries legibility (body, meta, snippets). Never set body in Grotesk.
- **Weights 400/500/600/700 only** — no thin, no black; the grotesque is expressive enough without weight extremes.
- **Paired-name aware**: name styles must gracefully wrap two names + "&" (couples) without truncating mid-name.
- **Negative tracking on display**: tighten -0.3 to -0.5pt on 22pt+ Grotesk for the confident, condensed feel.
- **Dynamic Type**: display, name, section, body, snippet scale; chip text, tab labels stay fixed (layout-sensitive pills).

## 4. Component Stylings

### Buttons

**Primary Button (Like / Continue)**
- Shape: Full pill, 500pt corner radius
- Background: `#E8FF63` (Acid Yellow) — the only acid fill on the screen
- Text/Icon: `#000000`
- Padding: 15pt vertical, 30pt horizontal (pill) — or 64pt circle in the Discover action row
- Font: Space Grotesk 16pt weight 600
- Pressed: background `#D4EB4F` + scale 0.98
- Discover variant: 64pt circle with `0 12px 30px -8px rgba(232,255,99,0.5)` glow

**Secondary Button (Send a Wink / Pink CTA)**
- Background: `#FF5C8A` (Desire Pink)
- Text: `#FFFFFF`
- Shape: pill, 500pt
- Padding: 15pt / 30pt
- Pressed: `#E0447A` + scale 0.98

**Outline Button (Skip / Pass)**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt `rgba(255,255,255,0.28)`
- Shape: pill 500pt (or 52pt circle in action row, bg `#1C1C1E`, 1pt `#2A2A2A`)
- Padding: 13pt / 24pt
- Pressed: background `#1C1C1E`

**Text Button (Edit Desires / inline action)**
- Background: none
- Text: `#E8FF63` (acid), Space Grotesk 15pt weight 600
- No underline
- Pressed: opacity 0.7

**Icon Button (top-bar hamburger / settings)**
- Size: 17pt glyph in 34pt circular `#1C1C1E` hit area
- Color: `#FFFFFF`, stroke 2pt
- Pressed: background `#262626`

### Profile Card (the core atom)

- Shape: rounded rect, **28pt** corner radius, full-bleed photo
- Fills most of the screen between top bar and action row
- Bottom gradient: `linear-gradient(180deg, transparent, rgba(0,0,0,0.72) 60%, rgba(0,0,0,0.92))` over bottom ~56%
- Body overlay (bottom-left, 20pt/22pt padding):
  - Name: Space Grotesk 26pt w700 `#FFFFFF` — supports "Robin & Sky"
  - Sub: Inter 14pt `rgba(255,255,255,0.78)` — "Couple · 31 & 29 · 2 km away"
  - Desire chips row: 7pt gap, wrapping
- Verified badge: top-right glass pill — `rgba(0,0,0,0.5)` + blur, 6/10pt padding, 999pt radius, acid checkmark 12pt + "Verified" Inter 11pt w600 `#FFFFFF`
- Photo dots (multi-photo): top, 4pt gap, active `#FFFFFF`, inactive `rgba(255,255,255,0.45)`

### Action Row (Discover)
- Centered, 22pt gap, padding 4/12pt
- Pass: 52pt circle, `#1C1C1E`, 1pt `#2A2A2A`, X glyph 22pt `#A8A8A8`
- Like: **64pt circle**, `#E8FF63`, heart 28pt filled `#000`, acid glow shadow
- Wink: 52pt circle, `#1C1C1E`, 1pt `#2A2A2A`, star 22pt filled `#C9B8FF`

### Desire / Identity Chips
- Shape: full pill 999pt
- Padding: 9pt vertical, 15pt horizontal (12pt size on card)
- States:
  - **Selected**: bg `#E8FF63`, text `#000000`, Inter 12pt w600
  - **Unselected**: bg `#1C1C1E`, text `#FFFFFF`, 1pt `#2A2A2A` border
  - **Emotional/Exploring**: bg `rgba(255,92,138,0.16)`, text `#FF5C8A`, 1pt `rgba(255,92,138,0.4)`
- Used in: profile card overlay, filter sheet, onboarding desire-picker

### Connections List
- Row: 12pt vertical padding, 1pt `#2A2A2A` bottom divider
- Avatar: 48pt circle (radial gradient placeholder)
- Name: Space Grotesk 15pt w600 `#FFFFFF` (supports paired)
- Snippet: Inter 13pt `#A8A8A8`, single line
- Timestamp: Inter 11pt `#6E6E6E`, trailing
- Unread: small acid dot or acid name weight bump

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: hamburger (filters) 17pt in 34pt circle `#1C1C1E`
- Center: screen title — Space Grotesk 22pt w700 `#FFFFFF` ("Discover")
- Trailing: settings/sort glyph in matching circle (acid dots accent)
- Background: transparent over black canvas

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(0,0,0,0.92)` + `saturate(180%) blur(20px)`, 0.5pt top border `#2A2A2A`
- Tabs (4): Discover · Likes · Connections · Profile
- Icon: 23pt, stroke 2pt
- Active: icon stroke `#E8FF63` + a 5pt acid dot under it — **no tint pill, no label color block**
- Inactive: `#6E6E6E`
- Labels optional (icons-only is valid Feeld pattern); dot is the active signal

### Input Fields

**Text Field (bio / message)**
- Height: 52pt (single-line) / auto (multiline)
- Background: `#1C1C1E`
- Border: none (focus: 1pt `#E8FF63`)
- Corner radius: 16pt
- Text: Inter 16pt `#FFFFFF`; placeholder Inter 16pt `#6E6E6E`

**Filter Sheet**
- Bottom sheet, `#121212`, 24pt top corner radius
- Grab handle: 36pt × 4pt `#2A2A2A`
- Contents: desire chips grid, distance slider (acid track), age range
- Apply button: full-width acid pill at bottom

### Distinctive Components

**Desire Chip System** — see Chips above. The defining Feeld component: identity and intent are expressed as togglable pills, never as dropdowns or radio lists.

**Paired Profile Header** — name area renders one OR two names with "&" and two ages; the design is couple/group-first, not single-first.

**Wink Gesture** — lilac star (super-interest) distinct from the acid Like; sends a soft signal before a full match.

**Verified Glass Pill** — frosted `rgba(0,0,0,0.5)` blur pill with acid checkmark; identity verification is a trust pillar for the audience.

**Liked-You Glow** — the acid Like button alone carries a soft acid drop-shadow halo, drawing the eye to the single primary action.

**Connections (not Matches)** — vocabulary and component naming use "Connection" throughout; the list, the empty state, and the chat header all say "Connection".

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 22, 24, 28, 32, 40, 48, 64
- Card-to-edge inset: 16pt
- Action row gap: 22pt
- Chip gap: 7pt (on card) / 10pt (in lists)

### Grid & Container
- iPhone: single full-bleed card column, 16pt side insets
- Card height: flexes to fill space between top bar and action row
- iPad: card max-width 520pt centered; Connections list max-width 600pt
- No multi-column content — Feeld is a single focused stack

### Whitespace Philosophy
- **One thing at a time**: Discover shows exactly one profile; no grid of faces
- **Black breathes**: generous true-black padding around the single card makes the photo and acid pop
- **Chips wrap, never truncate**: desire chips flow to multiple rows rather than clip
- **Action row isolated**: clear vertical space isolates Pass/Like/Wink from the card

### Border Radius Scale
- Square (0pt): full-bleed photo edges that meet the screen
- Medium (8pt): inline tags, small affordances
- Standard (12–16pt): input fields, small cards, sheet sub-cards
- Comfortable (24pt): bottom-sheet top corners
- Card (28pt): the signature profile card
- Pill (500pt): all buttons, all desire chips
- Circle (50%): avatars, action-row buttons

## 6. Depth & Elevation

Feeld is mostly flat — depth comes from the photo, the black canvas, and one glow.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Black canvas, list rows, chips |
| Card (Level 1) | Implicit via photo + 28pt radius over black | Profile card (no drop shadow needed) |
| Acid Glow (Level 2) | `0 12px 30px -8px rgba(232,255,99,0.5)` | The single acid Like button only |
| Sheet (Level 3) | `0 -8px 40px rgba(0,0,0,0.6)` | Filter / settings bottom sheets |
| Scrim | `rgba(0,0,0,0.45)` | Dim behind modals and sheets |

**Shadow Philosophy**: On a true-black canvas, conventional drop shadows are invisible — so Feeld doesn't fake them. Elevation reads from the 28pt rounded card sitting over black and from the **single acid glow** on the primary Like, which doubles as a visual "this is THE action" cue. Bottom sheets get a top-corner radius + a soft upward shadow as their only elevation tell.

### Motion
- **Card swipe (like/pass)**: 1:1 finger tracking; rotation ±8° tied to horizontal drag; release past 35% width commits with 220ms spring (damping 0.8); card exits in the swipe direction
- **Like tap (button)**: button scales 0.92 → 1.0 in 180ms ease-out; acid glow pulses; soft success haptic
- **Wink tap**: lilac star bounces (scale 1.0 → 1.25 → 1.0) 260ms; light haptic
- **Match/Connection made**: full-screen takeover, two avatars slide together from edges 320ms ease-out, acid confetti burst, "It's a Connection" Space Grotesk 32pt
- **Chip toggle**: instant fill swap + 120ms scale 0.96 → 1.0; selection haptic
- **Sheet present**: bottom sheet slides up 300ms ease-out; scrim fades in parallel
- **Tab switch**: active dot slides under new icon 200ms ease-out; no cross-fade of icons
- **Photo advance**: tap left/right edge → instant photo swap; dots update with no animation
- **Haptics**: soft impact on Like commit, light on Wink, selection on chip toggle, success on Connection

## 7. Do's and Don'ts

### Do
- Use true black (`#000000`) as the canvas — Feeld is dark-native, not "dark mode"
- Ration acid-yellow `#E8FF63` to exactly one primary action per screen
- Design couple/group-first — name area must hold two names + two ages gracefully
- Use the Desire chip system for identity and intent — togglable pills, three states
- Put the only glow on the acid Like button — it signals THE action
- Say "Connection", not "Match", everywhere (vocabulary is part of the brand)
- Round everything: 28pt cards, 500pt pill buttons, circular avatars
- Set personality type (display, names, sections, buttons) in Space Grotesk
- Set legibility type (body, meta, snippets) in Inter
- Surface the verified glass pill on the photo — verification is a trust pillar
- Keep the tab bar untinted except for the acid icon + 5pt dot on active

### Don't
- Don't introduce a light theme as a first-class mode — the app ships dark-only
- Don't use acid-yellow as a background behind paragraphs or as a second CTA fill
- Don't assume single-person profiles — never truncate the second name in a couple
- Don't use dropdowns/radio lists for desires — they are chips by design
- Don't add drop shadows to cards on black (invisible; the radius + photo carry it)
- Don't tint the whole active tab (pill/label block) — only the icon + small dot
- Don't set body text in Space Grotesk — Inter carries legibility
- Don't soften the pink toward romantic pastel — `#FF5C8A` stays saturated and adult
- Don't fill the screen with a grid of faces — Discover is one card at a time
- Don't replace "Connection" with "Match" — it changes the brand voice
- Don't use thin/black font weights — stay in 400–700

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card height compresses; chips wrap to 2 rows; action buttons 56/48pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island; top bar respects island, card starts below |
| iPhone 15/16 Pro Max | 430pt | Larger card; action buttons 68/56pt; name 28pt |
| iPad (portrait) | 768pt | Card max-width 520pt centered; black margins; Connections 600pt |
| iPad (landscape) | 1024pt+ | Discover card centered 520pt; optional split with Connections list |

### Dynamic Type
- Display, profile name, section, body, message snippet: full scale
- Desire chip text, tab labels, verified-pill text: FIXED (layout-sensitive pills)
- Card meta scales one step then truncates with ellipsis (never wraps past 2 lines)

### Orientation
- Discover stays portrait-locked on iPhone (card-swipe gesture)
- iPad supports landscape — card stays centered, Connections list can sit beside it
- Chat view rotates freely

### Touch Targets
- Action-row buttons: 52–64pt (well above 44pt)
- Desire chip: ≥ 32pt tall hit area
- Tab icon: 23pt glyph, 48pt hit
- Top-bar icon: 34pt circle hit
- Photo-advance tap zones: left/right 40% of card each

### Safe Area Handling
- Top: top bar + first card respect Dynamic Island / notch
- Bottom: tab bar + home indicator respected; action row sits above tab bar
- Sides: 16pt card inset
- Keyboard (chat): message bar rises with keyboard; list scrolls above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (dark-native, only mode)
- Surface 1 / 2 / 3: `#121212` / `#1C1C1E` / `#262626`
- Divider: `#2A2A2A`
- Text primary / secondary / tertiary: `#FFFFFF` / `#A8A8A8` / `#6E6E6E`
- Accent (acid): `#E8FF63` (pressed `#D4EB4F`) — one per screen
- Desire pink: `#FF5C8A` (pressed `#E0447A`)
- Lilac (Wink): `#C9B8FF`
- On-acid text: `#000000`
- Success / Error: `#66E0A3` / `#FF6B6B`

### Example Component Prompts
- "Build the Feeld Discover card in SwiftUI: full-bleed photo, 28pt corner radius, 16pt side insets, filling the space between top bar and action row. Bottom gradient `linear-gradient(transparent → rgba(0,0,0,0.72) at 60% → rgba(0,0,0,0.92))` over the bottom 56%. Overlay at bottom-left (20/22pt padding): name in Space Grotesk 26pt w700 `#FFFFFF` supporting two names ('Robin & Sky'); sub in Inter 14pt `rgba(255,255,255,0.78)` ('Couple · 31 & 29 · 2 km away'); a wrapping row of Desire chips (7pt gap). Top-right: a glass pill `rgba(0,0,0,0.5)` + blur, 999pt radius, acid checkmark 12pt + 'Verified' Inter 11pt w600."

- "Create the Feeld action row: centered, 22pt gap. Pass = 52pt circle `#1C1C1E` + 1pt `#2A2A2A`, X glyph 22pt `#A8A8A8`. Like = 64pt circle `#E8FF63`, filled heart 28pt `#000000`, drop-shadow `0 12px 30px -8px rgba(232,255,99,0.5)`. Wink = 52pt circle `#1C1C1E` + 1pt `#2A2A2A`, filled star 22pt `#C9B8FF`. On Like tap: scale 0.92→1.0 over 180ms ease-out + soft haptic."

- "Render a Feeld Desire chip: full pill (999pt), Inter 12pt w600, padding 9/15pt. Selected = bg `#E8FF63` text `#000000`. Unselected = bg `#1C1C1E` text `#FFFFFF` 1pt `#2A2A2A` border. Emotional = bg `rgba(255,92,138,0.16)` text `#FF5C8A` 1pt `rgba(255,92,138,0.4)`. Toggle: instant fill swap + 120ms scale 0.96→1.0 + selection haptic."

- "Build the Feeld Connections list row: 12pt vertical padding, 1pt `#2A2A2A` bottom divider. Leading 48pt circular avatar. Name in Space Grotesk 15pt w600 `#FFFFFF` (supports paired names). Snippet in Inter 13pt `#A8A8A8` single line. Trailing timestamp Inter 11pt `#6E6E6E`. Unread state: small acid `#E8FF63` dot."

- "Create the Feeld bottom tab bar: height 64pt + safe area, background `rgba(0,0,0,0.92)` + `saturate(180%) blur(20px)`, 0.5pt top border `#2A2A2A`. Four tabs: Discover, Likes, Connections, Profile, icons 23pt stroke 2pt. Active = icon stroke `#E8FF63` + a 5pt acid dot beneath; inactive `#6E6E6E`. No tint pill, no colored label block."

- "Build the Feeld filter sheet: bottom sheet `#121212`, 24pt top corner radius, 36×4pt `#2A2A2A` grab handle. Contents: a wrapping grid of Desire chips, a distance slider with acid `#E8FF63` track, an age range. Bottom: full-width acid pill 'Apply' (Space Grotesk 16pt w600 `#000000`). Present: slide up 300ms ease-out + scrim `rgba(0,0,0,0.45)` fade-in."

### Iteration Guide
1. Canvas is true black `#000000` — dark-native; do NOT build a light theme
2. Acid-yellow `#E8FF63` is rationed to ONE primary action per screen — never a paragraph background
3. Couples/groups are first-class — the name area holds two names + two ages, never truncated
4. Desires are togglable pill chips with three states (selected / unselected / emotional) — never dropdowns
5. The only glow in the UI is on the acid Like button — it marks THE action
6. Vocabulary is "Connection", not "Match" — list, empty state, chat header all say it
7. Type is two-font: Space Grotesk for personality (display/name/section/button), Inter for legibility (body/meta/snippet)
8. Everything is rounded — 28pt card, 500pt pill buttons, circular avatars/action buttons
9. Tab bar active = acid icon + 5pt dot only (no pill, no label block)
10. Pink `#FF5C8A` is the adult secondary emotional color — keep it saturated, not pastel
