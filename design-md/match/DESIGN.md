# Design System Inspiration of Match (iOS)

## 1. Visual Theme & Atmosphere

Match's iOS app is built around one hero object: the **full-bleed profile card**. Open the app and the entire screen — edge to edge, behind the status bar, above the tab strip — is one person's photo. There is no card border, no surrounding chrome, no list. The photo *is* the screen. Over the bottom third of that photo a dark scrim fades up so a 26pt bold name, an age, a verification badge, a job line, a distance line, and a row of frosted interest chips can sit legibly on top. This is the Match philosophy made visual: the product is *people*, so the interface gets out of the way and lets a face fill the glass. Everything else — the action dock, the tab bar, the Likes You grid — is supporting cast around that one full-bleed photograph.

The signature gesture surface is the **action dock**: a centered row of circular buttons floating over the bottom of the profile card — Rewind (gold), Pass (large, gray X), Super Like (blue star), Like (large, solid Match Red heart), and Boost (purple lightning). The Like button is the loudest object in the entire app: a 56pt solid `#E92434` (brightened to `#F0203E` on dark) circle with a white heart, deliberately the highest-contrast element on screen so the core action is unmissable. Tapping it (or swiping the card right) can trigger the other signature moment — the **"It's a Match!"** full-screen celebration, where two circular avatars overlap on a deep maroon-to-ink gradient under the red wordmark.

The color system is disciplined and monogamous: one brand red does almost all the work. **Match Red `#E92434`** (with the brighter web-energy variant `#F0203E`) is the wordmark, the Like button, the active tab, the match badge, the "It's a Match!" headline, and the primary CTA. There is no second brand hue. The only other accents are *functional*, not decorative: a calm green dot `#2ECC71` for "Active now", a blue `#1FB6FF` for the Super Like / verified-photo badge, a muted gold `#D6A75B` for Rewind and Premium, and a purple `#B36BD8` for Boost. Photography is always full-color and never tinted; the UI chrome is the only thing that goes dark.

Typography is a clean geometric sans (the brand uses a custom face in the Poppins / Proxima family) that prioritizes confident, friendly legibility over editorial flair. The wordmark is lowercase `match` in heavy 800 weight. Profile names are 26pt bold; ages drop to a lighter 500 weight beside the name; metadata and distance fall to 14pt regular secondary gray; interest chips are 12pt semibold inside frosted translucent pills. The hierarchy is photo-first, then name, then everything else — a dating-app rhythm, not a document or feed rhythm.

**Key Characteristics:**
- Full-bleed profile photo as the entire screen — no card frame, no surrounding chrome
- Bottom dark scrim fades up so name / age / job / distance / interest chips stay legible on photography
- Floating circular **action dock** — Rewind / Pass / Super Like / Like / Boost — over the photo bottom
- The **Like** button is the single loudest object: solid Match Red circle, white heart, largest contrast
- One disciplined brand red (`#E92434` / bright `#F0203E`) — no second brand color
- Functional accents only: green "Active now", blue Super Like / verified, gold Rewind / Premium, purple Boost
- **"It's a Match!"** full-screen celebration — two overlapping avatars, maroon-to-ink gradient, red wordmark
- **Likes You** grid — blurred locked tiles vs. one unlocked clear photo with a red heart badge
- Story-style segmented progress bars at the top of each profile card (photo carousel)
- Warm near-black dark canvas (`#141414`) — never pure black; photography never dims
- Frosted translucent interest chips with hairline white borders over the photo
- Minimal chrome: a 4-tab bottom bar (Discover / Likes / Messages / Profile), no top app bar weight

## 2. Color Palette & Roles

### Primary (Interactive)
- **Match Red** (`#E92434`): The brand red — wordmark, primary CTA, match badge, selected states (light mode).
- **Match Red Bright** (`#F0203E`): The energized variant — the Like button, active tab, and primary CTA on dark, where the slightly hotter red holds vibrance against dark photography.
- **Match Red Pressed** (`#C81B2C`): Pressed/active state for primary buttons.
- **Match Ink** (`#1A1A1A`): The near-black brand neutral — used as button text on red surfaces is white; `#1A1A1A` is the headline ink on light and the celebration gradient base.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas (settings, messages list, profile editor).
- **Surface Gray** (`#F6F4F4`): Grouped list backgrounds, search field, secondary surfaces.
- **Surface Pressed** (`#ECE8E8`): Pressed list rows.
- **Divider** (`#E7E2E2`): Hairline dividers between message rows and settings cells.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#141414`): Primary dark canvas — Match's warm near-black, NOT pure black.
- **Dark Surface 1** (`#1E1E1E`): Action-dock circles, cards, message bubbles, elevated surfaces.
- **Dark Surface 2** (`#2A2424`): Pressed rows, inset fields, secondary chips.
- **Dark Divider** (`#322C2C`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#1A1A1A`): Primary text on light surfaces — near-black, slightly warm.
- **Text Secondary Light** (`#6E6666`): Metadata, distance, captions on light.
- **Text Tertiary Light** (`#A39B9B`): Placeholder, disabled, very low emphasis.
- **Text Primary Dark** (`#F2EDED`): Primary text on dark surfaces.
- **Text Secondary Dark** (`#A89E9E`): Metadata, distance, captions on dark.
- **On-Photo White** (`#FFFFFF` / `rgba(255,255,255,0.9)`): All text rendered over the profile photo scrim.

### Functional Accents
| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| Like / Heart | `#E92434` | `#F0203E` | Like button, match heart badge, primary CTA |
| Active Now | `#2ECC71` | `#2ECC71` | Online presence dot, "Active now" |
| Super Like | `#1FB6FF` | `#1FB6FF` | Super Like star, verified-photo badge |
| Rewind / Premium | `#C8923F` | `#D6A75B` | Rewind button, Premium accents, Top Pick gold |
| Boost | `#9B4FC0` | `#B36BD8` | Boost lightning, Boost active glow |
| Pass | `#6E6666` | `#A89E9E` | Pass (X) glyph — neutral, not red |

### Semantic
- **Error / Destructive** (`#F2545B`): Unmatch / block / report confirmation, form errors.
- **Success** (`#2ECC71`): "Photo verified", profile-saved toast.
- **Warning** (`#E0A33D`): Incomplete-profile banner, photo-review pending.
- **Match Glow** (`#F0203E` at 30%): Soft radial glow behind the "It's a Match!" headline.

## 3. Typography Rules

### Font Family
- **Primary**: Match ships a custom geometric grotesque in the **Proxima Nova / Poppins** family — a friendly, confident humanist sans tuned for face-first dating UI. Use `Poppins` (Google Fonts, SIL OFL) as the closest free substitute.
- **Wordmark**: Lowercase `match`, weight 800, tight tracking — a fixed brand asset, never re-typeset by the app.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Proportional in names/ages; tabular only in distance and message timestamps.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wordmark | Poppins | 28pt | 800 | 1.0 | -0.4pt | Lowercase `match`, brand red |
| Profile Name | Poppins | 26pt | 700 | 1.1 | -0.3pt | On-photo, white, with age beside |
| Profile Age | Poppins | 22pt | 500 | 1.1 | -0.2pt | Lighter weight beside the name |
| Match Headline | Poppins | 26pt | 800 | 1.1 | 0.5pt | "It's a Match!" — brand red |
| Screen Title | Poppins | 22pt | 700 | 1.2 | -0.2pt | "Likes You", "Messages" |
| Section Header | Poppins | 18pt | 600 | 1.3 | -0.1pt | "About me", "Interests" |
| Body | Poppins | 16pt | 400 | 1.5 | 0pt | Bio text, message body |
| List Title | Poppins | 15pt | 600 | 1.3 | 0pt | Conversation name in messages list |
| Button | Poppins | 16pt | 700 | 1.0 | 0.2pt | Primary CTA label |
| Meta / Distance | Poppins | 14pt | 400 | 1.4 | 0pt | "Active now · 3 miles away" |
| Job / Subtitle | Poppins | 14pt | 500 | 1.4 | 0pt | "Product Designer · Brooklyn" |
| Interest Chip | Poppins | 12pt | 600 | 1.0 | 0.1pt | Frosted pill over photo |
| Tab Label | Poppins | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Timestamp | Poppins | 12pt | 400 | 1.0 | 0pt | Message time, last-active |

### Principles
- **Photo-first hierarchy**: The profile *photo* outranks all type — name is the second element, everything else is tertiary. Type never competes with the face.
- **Name bold, age light**: The name carries weight 700; the age drops to 500 beside it. This is a signature Match rhythm.
- **One red, used sparingly in type**: Brand red appears only on the wordmark and the "It's a Match!" headline — never on body, never on metadata.
- **On-photo legibility is non-negotiable**: All over-photo text sits on a bottom scrim and uses white with a subtle text shadow; never place dark text directly on a photo.
- **Friendly, not editorial**: Geometric humanist sans throughout — confident and warm, never a display serif, never condensed.
- **Dynamic Type**: Name, body, section headers scale; tab labels, interest chips, timestamps, story-bar text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Send Like / Continue / Get Match Premium)**
- Shape: Fully rounded pill, 999pt corner radius
- Background: `#E92434` light / `#F0203E` dark
- Text: `#FFFFFF`, Poppins 16pt weight 700
- Padding: 15pt vertical, 30pt horizontal
- Pressed: background `#C81B2C` + scale 0.98
- Premium variant: linear gradient `#F0203E → #B81DB0` (red-to-magenta), same pill shape

**Secondary / Outline Button (Skip / Maybe Later)**
- Background: transparent
- Text: `#1A1A1A` light / `#F2EDED` dark, Poppins 15pt weight 600
- Border: 1.5pt `#6E6666` light / `#A89E9E` dark
- Padding: 13pt vertical, 24pt horizontal
- Corner radius: 999pt (pill)
- Pressed: background `#F6F4F4` light / `#2A2424` dark

**Text Button (See who likes you / Edit profile)**
- Font: Poppins 14pt weight 600
- Color: `#E92434` light / `#F0203E` dark
- No background, no underline
- Pressed: 60% opacity

**Circular Action Button (the action dock)**
- Shape: perfect circle
- Default circles: `#FFFFFF` light / `#1E1E1E` dark with 1pt `#E7E2E2` / `#322C2C` border, soft drop shadow `0 6px 16px rgba(0,0,0,0.5)`
- Sizes: Rewind 44pt, Pass 56pt, Super Like 48pt, Like 56pt, Boost 44pt
- Glyph tints: Rewind gold `#D6A75B`, Pass gray `#A89E9E`, Super Like blue `#1FB6FF`, Boost purple `#B36BD8`
- **Like** is the exception: solid `#F0203E` fill, white heart glyph, no border — the loudest element
- Pressed: scale 0.92 + soft haptic

### Profile Card (the core atom)

The full-screen profile card is Match's hero component.
- Full-bleed photo: fills the entire safe area edge-to-edge (behind status bar, above tab bar)
- Corner radius: 20pt (a subtle inset rounding; the photo is not perfectly flush to screen edges)
- Story progress bars: row of segmented 3pt bars at top (12pt inset), `rgba(255,255,255,0.35)` inactive, `#FFFFFF` active — one segment per photo
- Bottom scrim: linear gradient `transparent → rgba(20,20,20,0.95)`, ~55% of card height
- Name block: name 26pt w700 white + age 22pt w500 + 17pt verified badge inline
- Job line: 14pt w500 `rgba(255,255,255,0.9)`
- Distance line: 7pt green dot + 14pt `rgba(255,255,255,0.75)` "Active now · 3 miles away"
- Interest chips: 12pt w600 white on `rgba(255,255,255,0.16)` with 1pt `rgba(255,255,255,0.28)` border, 999pt radius, backdrop blur
- Tap zones: left third = previous photo, right third = next photo, center = open full profile

### Likes You Tile

A grid (2-up or 3-up) of the people who liked you.
- Tile size: ~96 × 128pt, 14pt corner radius
- **Locked** (non-subscriber): the photo is heavily blurred (`blur(7px) scale(1.2)`), with a small `Liked you` pill (white text on `rgba(0,0,0,0.5)`, 999pt) bottom-center
- **Unlocked** (subscriber / mutual): clear full-color photo + a 22pt solid Match Red heart badge top-right
- Tapping a locked tile routes to the Premium upsell; tapping an unlocked tile opens the profile

### "It's a Match!" Celebration

The signature emotional moment.
- Full-screen overlay, background gradient `#2A1418 → #1A1A1A` (deep maroon to ink)
- Headline: "It's a Match!" — Poppins 26pt w800, brand red `#F0203E`, soft red glow behind
- Subtitle: "You and {name} liked each other" — 13pt secondary
- Two circular avatars (56pt) overlapping by ~14pt, each with a 3pt `#1A1A1A` ring
- Two CTAs below: solid-red "Send a Message" pill + outline "Keep Swiping" pill
- Entrance: avatars spring in from opposite sides; red heart confetti burst; success haptic

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `#FFFFFF` / `rgba(20,20,20,0.94)` with `backdrop-filter: blur(20px)` and a 0.5pt top border `#E7E2E2` / `#322C2C`
- Tabs (4): Discover, Likes, Messages, Profile
- Icon size: 23pt
- Active color: `#E92434` / `#F0203E` with filled icon variant
- Inactive: `#6E6666` / `#A89E9E`, outline icon
- Labels: Poppins 10pt w600, always shown
- Likes tab carries a numeric red badge when new likes arrive

**Top Bar (Discover)**
- Lightweight — no large nav bar
- Leading: lowercase `match` wordmark in brand red, 22pt w800
- Center: pill segmented control "Discover" / "Standouts" (active = white text)
- Trailing: filter / preferences icon (24pt, primary stroke)

### Input Fields

**Search / Message Composer**
- Height: 44pt
- Background: `#F6F4F4` light / `#2A2424` dark
- Border: none (focus adds 1.5pt `#E92434`)
- Corner radius: 999pt (pill)
- Leading icon 18pt secondary; placeholder 15pt secondary
- Send button: circular 36pt solid `#E92434`, white arrow, appears when text present

**Settings / Profile Fields**
- Grouped inset list, `#F6F4F4` / `#1E1E1E` card, 14pt corner radius
- Row height 52pt; label left primary, value/chevron right secondary
- Editable text fields: 1pt `#E7E2E2` underline that thickens to 1.5pt `#E92434` on focus

### Distinctive Components

**Action Dock** — see Buttons. The defining interaction surface: five floating circular buttons over the photo, the Like the loudest.

**Story Progress Bars** — segmented top bars on each profile card (one per photo); tap left/right to navigate the carousel; the active segment is solid white.

**Frosted Interest Chips** — translucent white pills with hairline borders and backdrop blur, sitting over the photo scrim; never solid-filled (would fight the photo).

**Likes You Blur Grid** — the monetization surface: blurred locked tiles vs. a single unlocked clear photo; the blur *is* the paywall.

**"It's a Match!" Modal** — full-screen maroon-to-ink celebration with overlapping avatars and the red wordmark headline.

**Verified Badge** — a 17pt blue (`#1FB6FF`) circle with a white check, inline after the profile name, signalling photo verification.

**Active-Now Dot** — a 7pt `#2ECC71` dot preceding "Active now"; the only green in the app, reserved strictly for presence.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Profile card inset: 16pt horizontal, 4pt top, 12pt above tab bar
- On-photo meta block: 18pt horizontal inset, 92pt up from card bottom (clears the dock)
- Action dock gap: 16pt between circles, centered
- Section padding (profile detail): 20pt horizontal, 24pt vertical between sections

### Grid & Container
- Discover: single full-bleed card, one at a time (swipe deck)
- Likes You: 2-column grid on iPhone (3-column on Plus/Max and iPad), 12pt gutter
- Messages: single-column list, full-width rows
- iPad: profile card centered at max-width 520pt; Likes grid expands to 4 columns

### Whitespace Philosophy
- **Photo edge-to-edge**: the profile photo claims the entire screen; whitespace lives only on the supporting screens (messages, settings)
- **Scrim instead of panels**: legibility comes from a gradient scrim over the photo, never an opaque info panel
- **Dock floats, doesn't dock a bar**: the action buttons hover over the photo with shadow, not pinned in a solid bottom bar
- **Generous touch targets**: the Like / Pass circles are 56pt so the core decision is effortless

### Border Radius Scale
- Square (0pt): full-bleed photo edges where flush, dividers
- Subtle (8pt): small tags, inline badges
- Standard (12pt): message bubbles, settings cards
- Comfortable (16pt): modals, Likes tiles (14pt), bottom sheets
- Card (20pt): the profile card inset rounding
- Large (28pt): hero upsell cards, premium sheets
- Pill (500pt): all buttons, search field, interest chips, segmented control
- Circle (50%): action-dock buttons, avatars, presence dot

## 6. Depth & Elevation

Match uses depth sparingly and purposefully — the photo is flat and dominant; only the floating controls and modals lift.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Full-bleed profile photo, list rows, settings cells |
| Scrim (Level 0.5) | Gradient `transparent → rgba(20,20,20,0.95)` | Bottom of profile card for text legibility |
| Floating (Level 1) | `0 6px 16px rgba(0,0,0,0.5)` | Action-dock circular buttons |
| Card (Level 2) | `0 12px 28px rgba(0,0,0,0.55)` | Profile card itself over the canvas |
| Modal (Level 3) | Full-screen overlay + `rgba(0,0,0,0.6)` dim | "It's a Match!", premium sheets, photo viewer |

**Shadow Philosophy**: Shadows exist to lift the *interactive* layer (the dock) off the photo and to make the swipe card feel like a physical object you can throw. They are deep (0.5-0.55 opacity) because they must register over dark photography. Content — the photo, the bio, the message list — is flat. On dark mode, shadows are even deeper and the dock circles add a 1pt `#322C2C` border as a secondary elevation cue.

### Motion
- **Card swipe**: 1:1 finger tracking; the card rotates up to ~12° and translates with the drag; release past ~35% width commits with a 250ms spring (damping 0.78); a "LIKE" / "NOPE" / "SUPER LIKE" stamp fades in proportional to drag distance
- **Like tap**: the heart scales 1.0 → 1.25 → 1.0 over 280ms with a soft haptic; card flies off-screen right in 300ms ease-out
- **It's a Match! entrance**: overlay fades in 200ms; two avatars spring in from opposite edges (response 0.4, damping 0.7); red heart confetti burst; success haptic (`.notificationOccurred(.success)`)
- **Story-bar tap**: instant segment switch with a 120ms cross-fade between photos
- **Tab switch**: instant; the active icon swaps to its filled variant with a 150ms scale pop
- **Likes grid unlock**: on subscribe, blurred tiles cross-fade to clear over 400ms with a staggered 40ms delay per tile
- **Boost active**: a pulsing purple radial glow behind the profile card, 1.6s ease-in-out loop
- **Haptics**: soft impact on swipe-commit and dock tap; success notification on a match; light tick on story-bar navigation

## 7. Do's and Don'ts

### Do
- Make the profile photo full-bleed — edge to edge, the entire screen IS the photo
- Use a bottom gradient scrim for on-photo text legibility — never an opaque info panel
- Keep the Like button the single loudest object — solid Match Red circle, white heart, largest size
- Use exactly one brand red (`#E92434` / bright `#F0203E` on dark) — no second brand color
- Reserve green strictly for presence ("Active now") and blue strictly for Super Like / verified
- Float the action dock over the photo with shadow — five circles, the Like the biggest
- Set the name bold (700) and the age light (500) right beside it — the signature Match name rhythm
- Use frosted translucent interest chips over the photo — hairline white border, backdrop blur
- Make "It's a Match!" a full-screen emotional moment — overlapping avatars, maroon-to-ink, red headline
- Use the blur as the paywall on Likes You — locked tiles blurred, unlocked tiles clear with a red badge
- Use a warm near-black (`#141414`) for dark mode — never pure black; never dim the photography

### Don't
- Don't frame the profile photo in a small card with margins — it's full-bleed, not a feed item
- Don't put dark text directly on a photo without the scrim — legibility comes from the gradient
- Don't introduce a second brand accent color — Match is monogamous with its red
- Don't tint the Like red elsewhere as decoration — it belongs to the action, the wordmark, and the match headline only
- Don't use green or blue decoratively — they are functional (presence / Super Like / verified) only
- Don't pin the action buttons in a solid opaque bottom bar — they float over the photo
- Don't make the age the same weight as the name — the weight drop (700 → 500) is intentional
- Don't fill interest chips with solid color — translucent frosted only, so the photo reads through
- Don't make the match celebration a small toast — it's the emotional peak; go full-screen
- Don't show locked Likes-You photos clearly — the blur is the entire monetization mechanic
- Don't use a condensed or serif display face — Match's voice is a friendly geometric sans
- Don't animate the swipe linearly — it must rotate and spring like a thrown physical card

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Profile card fills screen; interest chips wrap to 2 rows; dock circles shrink ~8% |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Story bars clear the Dynamic Island; name block sits above the dock |
| iPhone 15/16 Pro Max | 430pt | Larger name (28pt); Likes grid → 3 columns |
| iPad (portrait) | 768pt | Profile card centered max-width 520pt with darkened backdrop; Likes grid → 4 columns |
| iPad (landscape) | 1024pt+ | Two-pane: Discover card left, conversation/profile detail right |

### Dynamic Type
- Profile name, bio body, section headers, message body: full scale
- Interest chips, tab labels, story-bar captions, timestamps, distance line: FIXED (layout-sensitive)
- The name/age inline pair scales together, preserving the weight contrast

### Orientation
- iPhone: portrait-locked on Discover (the swipe card is portrait by nature)
- Messages, settings, profile editor: rotate freely
- iPad: full rotation with the two-pane split

### Touch Targets
- Like / Pass circles: 56pt (largest — core decision)
- Super Like / Rewind / Boost circles: 44–48pt
- Story-bar tap zones: full left/right thirds of the card
- Tab bar icons: 23pt glyph, 44pt hit
- Interest chips: display-only (not interactive on the card)

### Safe Area Handling
- Top: story progress bars and the wordmark respect the safe area / Dynamic Island
- Bottom: the action dock and tab bar respect the home indicator; the dock floats 16pt above it
- The photo extends *into* the safe areas (full-bleed) but all text/controls stay inside
- Keyboard: message composer rises above the keyboard; the conversation scrolls

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#141414`
- Surface (dark): `#1E1E1E`
- Divider: `#E7E2E2` light / `#322C2C` dark
- Text primary: `#1A1A1A` light / `#F2EDED` dark
- Text secondary: `#6E6666` light / `#A89E9E` dark
- Match Red: `#E92434`
- Match Red Bright (dark / Like): `#F0203E`
- Match Red Pressed: `#C81B2C`
- Active Now (green): `#2ECC71`
- Super Like / Verified (blue): `#1FB6FF`
- Rewind / Premium (gold): `#D6A75B`
- Boost (purple): `#B36BD8`
- On-photo scrim: `transparent → rgba(20,20,20,0.95)`
- Match celebration gradient: `#2A1418 → #1A1A1A`

### Example Component Prompts
- "Build the Match profile card in SwiftUI: a full-bleed photo filling the entire safe area (behind status bar, above tab bar), 20pt corner radius. Overlay a bottom linear-gradient scrim from `transparent` to `rgba(20,20,20,0.95)` over the lower 55%. At top, a row of segmented 3pt progress bars (one per photo), inactive `rgba(255,255,255,0.35)`, active `#FFFFFF`, 12pt insets. In the lower area: name in Poppins 26pt w700 white, age in 22pt w500 beside it, a 17pt blue `#1FB6FF` verified check inline; below, job in 14pt w500 `rgba(255,255,255,0.9)`; below that a 7pt `#2ECC71` dot + 'Active now · 3 miles away' in 14pt `rgba(255,255,255,0.75)`; then a wrapped row of frosted chips — 12pt w600 white on `rgba(255,255,255,0.16)` with 1pt `rgba(255,255,255,0.28)` border, 999pt radius, backdrop blur."

- "Create the Match action dock: a horizontally-centered row of 5 circular buttons floating 16pt above the bottom of the profile card, 16pt gaps. Rewind 44pt, Pass 56pt, Super Like 48pt, Like 56pt, Boost 44pt. Default circles `#1E1E1E` with 1pt `#322C2C` border and `0 6px 16px rgba(0,0,0,0.5)` shadow; glyph tints — Rewind `#D6A75B`, Pass `#A89E9E`, Super Like `#1FB6FF`, Boost `#B36BD8`. The Like button is solid `#F0203E` with a white heart and NO border — the loudest element. Pressed: scale 0.92 + soft haptic."

- "Build the Match 'It's a Match!' celebration: full-screen overlay, background linear gradient `#2A1418 → #1A1A1A`. Centered headline 'It's a Match!' in Poppins 26pt w800 `#F0203E` with a soft red radial glow behind. Subtitle 'You and Sofia liked each other' in 13pt `#A89E9E`. Two 56pt circular avatars overlapping by 14pt, each with a 3pt `#1A1A1A` ring. Below: a solid `#F0203E` pill 'Send a Message' and an outline pill 'Keep Swiping'. Entrance: avatars spring from opposite edges (response 0.4, damping 0.7), red heart confetti, success haptic."

- "Render the Match Likes You grid: 2-column grid, 12pt gutter, tiles ~96×128pt with 14pt radius. Locked tiles: photo with `blur(7px) scale(1.2)` and a bottom-center pill 'Liked you' (white text on `rgba(0,0,0,0.5)`, 999pt). One unlocked tile: clear full-color photo with a 22pt solid `#E92434` heart badge top-right. Tap locked → Premium upsell; tap unlocked → profile."

- "Build the Match bottom tab bar: 64pt + safe area, background `rgba(20,20,20,0.94)` with 20px backdrop blur and a 0.5pt `#322C2C` top border. 4 tabs — Discover, Likes, Messages, Profile — 23pt icons, labels Poppins 10pt w600 always shown. Active tint `#F0203E` with filled icon; inactive `#A89E9E` outline. The Likes tab shows a numeric red badge for new likes."

- "Create the Match primary CTA: a fully-rounded pill (999pt), background `#E92434` (`#F0203E` on dark), label in Poppins 16pt w700 white, 15pt/30pt padding. Pressed: `#C81B2C` + scale 0.98. Premium variant: same pill with a linear gradient `#F0203E → #B81DB0`."

### Iteration Guide
1. The profile photo is FULL-BLEED — the entire screen is the photo, no card frame, no margins
2. On-photo legibility comes from a bottom gradient scrim — never an opaque info panel
3. The Like button is the single loudest object — solid Match Red circle, white heart, biggest size
4. Exactly one brand red — `#E92434` light, brightened to `#F0203E` on dark for the Like/active states
5. Green is presence only, blue is Super Like / verified only, gold is Rewind / Premium, purple is Boost — never decorative
6. Name is bold 700; age is light 500 right beside it — the signature Match name rhythm
7. Interest chips are frosted translucent pills (white text, hairline border, backdrop blur) — never solid
8. "It's a Match!" is a full-screen emotional peak — overlapping avatars, maroon-to-ink gradient, red headline
9. The blur is the paywall — locked Likes-You tiles blurred, unlocked clear with a red heart badge
10. Dark mode is warm near-black `#141414` — never pure black; photography never dims
