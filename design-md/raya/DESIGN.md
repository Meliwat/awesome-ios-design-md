# Design System Inspiration of Raya (iOS)

## 1. Visual Theme & Atmosphere

Raya's iOS app is the most uncompromisingly monochrome interface in consumer dating. There is no brand color — none. The canvas is **pure black `#000000`** (not a warm near-black, not a charcoal) and the only "accent" is **pure white `#FFFFFF`**. Every UI element — buttons, bars, icons, type — is black, white, or a gray between. The single source of color in the entire experience is the *member's own photography and video*, which the interface deliberately recedes behind. Raya treats itself like a gallery frame: the art is the person, and the frame is rendered in the quietest possible chrome so nothing competes with the member's curated images. This radical restraint *is* the brand — exclusivity expressed as the absence of decoration.

The signature object is the **full-bleed slideshow profile**. A member's profile is not a static card; it is a sequence of full-screen photos and short videos that auto-advances like an Instagram story, set to **the member's chosen song**. At the top: segmented story bars (2.5pt, white-active, dimmed-inactive) that fill as the slideshow plays and let you tap left/right to scrub. Just below: a small white **music ticker** — a tiny vinyl/note glyph, the artist and track ("Frank Ocean — Pink + White"), and a 4-bar equalizer animating to the beat. At the bottom, over a black gradient scrim: an editorial caption — the member's first name in 28pt, an uppercase role line ("DIRECTOR · FILM & MUSIC"), a short bio, and a location. The slideshow + song is the emotional core: Raya is curating a *vibe*, not a profile, and the music makes it feel cinematic.

The color system has, functionally, two colors. **Pure black `#000000`** is the canvas; **pure white `#FFFFFF`** is the wordmark, the single primary button, the active story bar, the heart outline, and primary text. Everything else is a gray on the black-to-white axis: surfaces lift only to `#0D0D0D`/`#161616`, dividers are `#242424`, hairlines `#2E2E2E`, secondary text `#9A9A9A`, tertiary `#5E5E5E`. There is no success green, no like-red, no premium gold — a destructive `#E5484D` exists but is used rarely and reluctantly. Depth is drawn with 1px hairlines, never with shadow or tint. The discipline is total: if it isn't the member's photo, it has no hue.

Typography is a clean, low-contrast neo-grotesque (the app uses a custom face; **Inter** is the closest free substitute) set with restraint. The wordmark is lowercase `raya`, weight 600. Profile names are 28pt semibold white. Role lines are 13pt uppercase, letter-spaced — the editorial-masthead tic that signals "this person is a creative professional." Body and bio sit at 15pt regular. Eyebrows and any labels are 11pt uppercase tracked +1.6px. There is no display serif, no flourish — the type is as quiet as the palette, because the member's images are the only thing meant to be loud.

**Key Characteristics:**
- Pure black `#000000` canvas (not warm, not charcoal) — a gallery frame for the member's images
- Zero brand color — the ONLY accent is pure white `#FFFFFF`; all UI is black/white/gray
- The only color anywhere is the member's own full-bleed photography and video
- **Slideshow profile** — full-screen photos/videos auto-advancing like a story, set to the member's chosen song
- Segmented story bars (2.5pt, white-active) at the top — tap left/right to scrub
- **Music ticker** — tiny note glyph + artist/track + animated 4-bar equalizer over the photo
- Editorial caption — first name 28pt + UPPERCASE role line + short bio + location, over a black scrim
- No drop shadows — depth is 1px hairlines (`#2E2E2E`) and tonal surface lifts only
- Minimal action affordances — Skip / a white-outline Heart / Note; no big colored swipe deck
- Lowercase `raya` wordmark; uppercase letter-spaced role lines and eyebrows (editorial-masthead voice)
- Permanently dark — there is no light mode, by design
- Restraint as exclusivity — the absence of decoration is the brand statement

## 2. Color Palette & Roles

### Primary (Interactive)
- **Pure White** (`#FFFFFF`): The single accent — wordmark, primary button, active story bar, heart outline, primary text, all active icons.
- **White Pressed** (`#D8D8D8`): Pressed state of the white primary button.
- **Pure Black** (`#000000`): The canvas; also the text/ink on a white surface (the primary button label).

### Canvas & Surfaces (the only theme — permanently dark)
- **Pure Black Canvas** (`#000000`): Primary canvas — true black, never warmed.
- **Surface 1** (`#0D0D0D`): Sheets, cards, settings rows — the first, barely-perceptible lift.
- **Surface 2** (`#161616`): Inset fields, pressed rows, secondary surfaces.
- **Divider** (`#242424`): Hairline dividers between settings rows and sections.
- **Hairline** (`#2E2E2E`): The 1px border on chips, fields, and the tab strip — the only depth cue.

### Text
- **Text Primary** (`#FFFFFF`): Names, headings, primary copy — pure white.
- **Text Secondary** (`#9A9A9A`): Metadata, captions, inactive labels.
- **Text Tertiary** (`#5E5E5E`): Disabled, placeholder, very low emphasis.
- **On-Photo White** (`rgba(255,255,255,0.92)`): Caption text over a photo (slightly softened so it doesn't glare).
- **On-Photo Dim** (`rgba(255,255,255,0.6)`): Location / least-emphasis text over a photo.

### Accent (there is, by design, only one)
| Role | Color | Use |
|------|-------|-----|
| Accent (the only one) | `#FFFFFF` | Wordmark, primary CTA, active story bar, heart outline, active tab/icons |
| Accent pressed | `#D8D8D8` | White button pressed |
| Scrim | `linear-gradient(transparent → rgba(0,0,0,0.92))` | Bottom of every slideshow photo for caption legibility |

### Semantic
- **Error / Destructive** (`#E5484D`): Used reluctantly — leave-Raya / report / payment-failed confirmation only. The only non-monochrome pixel in the UI, and it should appear rarely.
- **Success**: There is no success color — confirmation is communicated with white text and a subtle check, never a green.
- **Disabled**: `#5E5E5E` on `#0D0D0D` — a dim gray, never a tinted state.
- **Member Media**: full-color, full-bleed — the *only* intentional color in the product; the UI must never tint, filter, or frame it with a colored border.

## 3. Typography Rules

### Font Family
- **Primary**: A clean, low-contrast neo-grotesque (the app ships a custom face in the **Inter / Söhne / Neue Haas Grotesk** family). Use `Inter` (Google Fonts, SIL OFL) as the closest free substitute.
- **Wordmark**: Lowercase `raya`, weight 600 — a fixed brand asset, never re-typeset.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **No second family**: there is no serif, no mono, no display face — one neo-grotesque does everything (mirroring the one-color discipline).
- **Numerals**: tabular in distance and timestamps; proportional elsewhere.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wordmark | Inter | 30pt | 600 | 1.0 | -0.5pt | Lowercase `raya`, pure white |
| Profile Name | Inter | 28pt | 600 | 1.1 | -0.4pt | On-photo, white, the caption headline |
| Section Title | Inter | 20pt | 600 | 1.2 | -0.2pt | "Discover", "Messages" |
| Subtitle | Inter | 16pt | 500 | 1.3 | 0pt | Section intro copy |
| Body / Bio | Inter | 15pt | 400 | 1.55 | 0pt | Bio text, message body |
| Role Line | Inter | 13pt | 500 | 1.3 | 0.4pt | UPPERCASE — "DIRECTOR · FILM & MUSIC" |
| Music Ticker | Inter | 11pt | 500 | 1.0 | 0.2pt | "Frank Ocean — Pink + White" over photo |
| Meta | Inter | 13pt | 400 | 1.3 | 0pt | "Los Angeles · 4 mi" |
| Eyebrow / Label | Inter | 11pt | 600 | 1.0 | 1.6pt | UPPERCASE — "MEMBERS ONLY" |
| Button | Inter | 14pt | 600 | 1.0 | 0.3pt | Primary / secondary label |
| Caption | Inter | 12pt | 400 | 1.3 | 0pt | Timestamps, helper text |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.4pt | If a tab strip is shown |

### Principles
- **One family, like one color**: a single neo-grotesque carries everything — the typographic equivalent of the monochrome discipline. Never introduce a serif or display face.
- **Uppercase role lines are the editorial tic**: the letter-spaced uppercase role ("DIRECTOR · FILM & MUSIC") is what makes Raya feel like a curated creative directory — always uppercase, always tracked.
- **Lowercase wordmark, uppercase chrome**: the wordmark is intentionally lowercase and quiet; eyebrows and role lines are uppercase. Names and bios are mixed-case.
- **No bold drama**: weights stay in the 400–600 band — there is no 800/900 heavy. Restraint in weight mirrors restraint in color.
- **On-photo text is softened white**: caption text over an image uses `rgba(255,255,255,0.92)` (and `0.6` for least-emphasis), never pure `#FFFFFF`, so it sits *in* the image rather than glaring over it.
- **Dynamic Type**: name, body, section titles, bio scale; story-bar text, music ticker, eyebrows, role lines, tab labels stay fixed (layout-sensitive over media).

## 4. Component Stylings

### Buttons

**Primary Button (Like / Continue / Request Membership)**
- Shape: Fully rounded pill, 999pt corner radius
- Background: `#FFFFFF` (the only filled element in the UI)
- Text: `#000000`, Inter 14pt weight 600, +0.3px tracking
- Padding: 15pt vertical, 30pt horizontal
- Pressed: background `#D8D8D8` + scale 0.99
- One per screen — white fill is rationed exactly like a brand color would be

**Secondary / Outline Button (Skip)**
- Background: transparent
- Text: `#FFFFFF`, Inter 14pt weight 600
- Border: 1px `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 999pt (pill)
- Pressed: background `rgba(255,255,255,0.08)`

**Ghost Button (Maybe Later)**
- Background: transparent
- Text: `#9A9A9A`, Inter 14pt weight 600
- Border: 1px `#2E2E2E` (hairline)
- Corner radius: 999pt
- Pressed: border brightens to `#3E3E3E`

**Text Button (View Profile)**
- Font: Inter 12pt weight 600, UPPERCASE, +1.4px tracking
- Color: `#FFFFFF`
- No background, no underline
- Pressed: `#9A9A9A`

### Slideshow Profile (the core atom)

The defining component — a member presented as a music-scored full-screen slideshow.
- Full-bleed: each slide is a full-screen photo or short video, edge to edge
- **Story bars**: row of segmented 2.5pt bars at the top safe-area (16pt insets, 5pt gaps); active/played = `#FFFFFF`, unplayed = `rgba(255,255,255,0.3)`; each segment fills over the slide's duration; tap left/right thirds to scrub
- **Music ticker**: directly under the bars — a 13pt vinyl/note glyph + artist/track in Inter 11pt w500 `rgba(255,255,255,0.85)` + a 4-bar white equalizer animating to the beat
- **Bottom scrim**: linear gradient `transparent → rgba(0,0,0,0.92)` over the lower ~62%
- **Caption** (over the scrim, ~96pt up from bottom):
  - Name: Inter 28pt w600 white
  - Role line: Inter 13pt w500 UPPERCASE `rgba(255,255,255,0.92)` +0.4px — "DIRECTOR · FILM & MUSIC"
  - Bio: Inter 15pt `rgba(255,255,255,0.72)`, 1–2 lines
  - Location: a 12pt pin glyph + Inter 13pt `rgba(255,255,255,0.6)` — "Los Angeles · 4 mi"
- **Actions** (bottom-center, ~18pt from edge): Skip (26pt white-90% X, no circle), Heart (60pt circle, 1px white border, white heart — the focal action), Note (24pt white-90% message glyph, no circle)

### Music Ticker

The signature flourish that makes Raya feel cinematic.
- Position: top of the slideshow, under the story bars
- Glyph: a 13pt vinyl-record or musical-note icon, white, 2px stroke
- Text: "{Artist} — {Track}" in Inter 11pt w500 `rgba(255,255,255,0.85)`
- Equalizer: 4 white bars (2px wide, 1px radius), heights animating in a loop to imply playback
- Tapping it can expand to the full track / open in the linked music service

### Member Chip

Interest / scene tags on the expanded profile.
- Pill, 999pt radius, 8pt/16pt padding, Inter 12pt w500
- Default: transparent fill, 1px `#2E2E2E` border, `#FFFFFF` text
- Selected/primary: solid `#FFFFFF` fill, `#000000` text (the only place a chip inverts)
- No color states — selection is the black/white inversion only

### Navigation

**Top Nav (Slideshow)**
- Effectively chromeless — the story bars *are* the top nav
- Optional: a lowercase `raya` wordmark centered for 1.5s on entry, then fades out so the media is unobstructed
- Trailing (on tap-hold/expand): a fine-line ⋯ for profile actions (report, share, hide)

**Bottom Tab Bar (when shown — Messages/Profile screens)**
- Height: 56pt + safe area
- Background: `#000000` opaque (no blur — true black is its own surface) with a 1px `#2E2E2E` top hairline
- Tabs: Discover, Messages, Profile (minimal — 3, not 5)
- Icon size: 22pt, 1.5px fine-line stroke, never filled
- Active: `#FFFFFF`; inactive: `#5E5E5E`
- Labels: Inter 10pt w500, +0.4px; no active pill (the white tint alone signals selection)

### Input Fields

**Message Composer**
- A single transparent field with a 1px `#2E2E2E` bottom rule only (no box)
- Focus: the rule brightens to 1px `#FFFFFF`
- Placeholder: Inter 14pt `#5E5E5E`
- Send affordance: a white text "SEND" (uppercase, +1px) — no filled button in conversation

**Settings / Profile Fields**
- Grouped on `#0D0D0D` with 1px `#242424` dividers (no card fill, no rounding on rows)
- Row: label left `#FFFFFF`, value/chevron right `#9A9A9A`
- Editable: a `#2E2E2E` underline that brightens to `#FFFFFF` on focus

### Distinctive Components

**Slideshow Profile** — the music-scored, story-paced full-screen member presentation. The product.

**Music Ticker** — the white note glyph + artist/track + equalizer; the cinematic signature.

**Story Bars** — the segmented 2.5pt white progress bars that pace the slideshow and enable scrubbing.

**Editorial Caption** — the name + UPPERCASE role line + bio + location over the black scrim; the curated-creative voice.

**Monochrome Inversion Chip** — interest pills whose only "state" is black-on-white vs white-outline; the system's single interaction color cue.

**The White Heart** — the single white-outline circular focal action; the only "brand" moment, kept as an outline, never a fill.

**Hairline System** — the 1px `#2E2E2E` borders that replace every shadow; the black-on-black depth language.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 64
- Story bars: 16pt horizontal insets, 5pt gaps, top safe-area + 6pt
- Caption block: 20pt horizontal insets, ~96pt up from the bottom (clears the actions)
- Action row: bottom safe-area + 18pt, 56pt gaps between the three affordances
- Settings: 16pt row insets, full-bleed `#242424` dividers

### Grid & Container
- Discover: one full-bleed slideshow at a time (a curated sequence, not a deck/feed)
- Messages: single-column list, full-width rows on `#000000`
- Profile (expanded): single-column, photo grid + chips + bio on black
- iPad: the slideshow stays full-bleed centered with a max-width 560pt frame on a pure-black surround

### Whitespace Philosophy
- **Black is the whitespace**: the negative space is literally `#000000` — emptiness is the aesthetic, not a lack
- **Media is uninterrupted**: chrome (wordmark, even the ticker) fades so the member's photo/video is unobstructed
- **Hairlines, not panels**: structure is the 1px `#2E2E2E` rule; never a colored or filled container
- **The caption breathes at the bottom**: text is anchored low over the scrim so the upper image stays clear

### Border Radius Scale
- Square (0pt): settings rows, dividers, full-bleed media
- Subtle (4pt): inline tags
- Standard (8pt): small thumbnails, the music-service expand card
- Comfortable (12pt): bottom sheets, modals
- Large (16–24pt): media thumbnails in the expanded profile grid
- Pill (500pt): all buttons, member chips
- Circle (50%): the heart action, avatars in messages

## 6. Depth & Elevation

Raya has no shadow and no elevation system in the conventional sense — depth is pure black-on-black with hairlines.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | `#000000`, no border | The canvas, full-bleed media |
| Scrim (Level 0.5) | `transparent → rgba(0,0,0,0.92)` | Bottom of every slideshow slide for caption legibility |
| Surface (Level 1) | `#0D0D0D`, optional 1px `#242424` divider | Settings groups, sheets, message list |
| Hairline (Level 1.5) | 1px `#2E2E2E` border | Chips, fields, tab strip top — the only "edge" cue |
| Sheet (Level 2) | `#0D0D0D` + 1px `#2E2E2E` top border + `rgba(0,0,0,0.6)` scrim behind | Bottom sheets, profile-action menu |
| Modal (Level 3) | `#0D0D0D` + full `rgba(0,0,0,0.85)` dim | Membership, leave-Raya confirmation |

**Shadow Philosophy**: There are no `box-shadow`s in Raya. A shadow implies light and material; Raya's aesthetic is a black void in which the member's media glows. Separation between a sheet and the canvas comes from the faint tonal lift (`#0D0D0D` vs `#000000`) plus a 1px `#2E2E2E` hairline at the seam. Because the canvas is true black, even this lift is barely perceptible — and that subtlety is intentional. Never add a shadow; never tint a surface to fake elevation.

### Motion
- **Slideshow auto-advance**: each slide holds ~5s (photos) / video length (videos); the active story bar fills linearly over that duration; on completion, the next slide cross-fades over 250ms
- **Story-bar scrub**: tap right third → next slide instant cut + bar jumps full; tap left third → previous; press-and-hold pauses (bar freezes, music ducks)
- **Music ticker**: the 4-bar equalizer loops a subtle height animation (~600ms) implying playback; on track change, the text cross-fades (180ms)
- **Caption entrance**: when a member's first slide loads, the caption fades + rises 8pt over 300ms ease-out (so it feels like it settles into the image)
- **Like (white heart)**: the outline heart fills white briefly (200ms), scales 1.0 → 1.1 → 1.0, then the next member's slideshow cross-fades in — no confetti, no colored burst
- **Wordmark intro**: on app open, `raya` fades in centered (400ms), holds 1.1s, fades out (400ms) revealing the slideshow — a cinematic cold-open
- **Tab switch**: instant; the active icon/label tint cross-fades white over 150ms (no pill slide)
- **Sheet present**: bottom sheet slides up 300ms ease-out over a `rgba(0,0,0,0.6)` dim
- **Haptics**: a soft impact on like and on slide-scrub; nothing buzzy — touch is as restrained as the palette

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — true black, never warmed or charcoal
- Treat pure white `#FFFFFF` as the only accent — wordmark, one button, active bar, heart outline
- Let the member's photography/video be the ONLY color in the entire experience
- Build the profile as a full-bleed slideshow paced by segmented white story bars
- Score the slideshow with the member's chosen song and show a small white music ticker
- Set the role line in UPPERCASE letter-spaced Inter ("DIRECTOR · FILM & MUSIC") — the editorial tic
- Anchor the caption low over a `transparent → rgba(0,0,0,0.92)` scrim
- Replace every shadow with a faint surface lift + 1px `#2E2E2E` hairline
- Keep the wordmark lowercase and quiet; let it fade so media is unobstructed
- Keep the white heart an OUTLINE, never a fill — restraint is the brand
- Use one neo-grotesque family for everything — the typographic mirror of the one-color rule
- Keep it permanently dark — there is no light mode, and that is intentional

### Don't
- Don't introduce ANY brand color — no like-red, no premium gold, no success green
- Don't warm the black — `#000000` exactly; `#0A0A0A`/`#121212` are too soft for Raya
- Don't add drop shadows — depth is black-on-black with hairlines, never light
- Don't tint, filter, or colored-border the member's media — it is the only color and must stay untouched
- Don't make the profile a static card — it is a music-scored, story-paced slideshow
- Don't drop the music ticker — the song is core to the curated-vibe experience
- Don't set role lines in mixed-case — they are UPPERCASE letter-spaced, always
- Don't fill the heart or any control with white by default — white fill is rationed to one CTA
- Don't introduce a serif or display face — one neo-grotesque, like one color
- Don't add a Material/iOS active-tab pill — the white tint alone signals selection
- Don't use a saturated success/like state — confirmation is white text + a subtle check, never green
- Don't crowd the media with chrome — let the wordmark and ticker recede; black is the whitespace

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Caption bio truncates to 1 line; story bars stay 2.5pt; name 26pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Story bars + music ticker clear the Dynamic Island (sit below it) |
| iPhone 15/16 Pro Max | 430pt | Larger name (30pt); bio gets a 2nd line |
| iPad (portrait) | 768pt | Slideshow centered in a max-width 560pt frame on a pure-black surround |
| iPad (landscape) | 1024pt+ | Two-pane: slideshow left, expanded profile / message thread right, both pure black |

### Dynamic Type
- Profile name, bio, section titles, message body: full scale
- Story-bar text, music ticker, role line, eyebrows, tab labels: FIXED (layout-sensitive over media)
- The caption block reflows upward if the bio scales, never overlapping the action row

### Orientation
- iPhone: portrait-locked on Discover (the slideshow is portrait/full-screen by nature)
- Messages, Profile, settings: rotate freely
- iPad: full rotation with the two-pane split

### Touch Targets
- Heart action: 60pt circle (the focal action — largest)
- Skip / Note: 44pt hit (26/24pt glyph, no circle but generous tap area)
- Story-bar scrub: full left / right thirds of the screen; center press-hold to pause
- Music ticker: 44pt minimum tap height to expand
- Tab icons: 22pt glyph, 44pt hit

### Safe Area Handling
- Top: story bars + music ticker sit below the safe area / Dynamic Island (never under it)
- Bottom: the action row and any tab strip respect the home indicator; the media is full-bleed *behind* them
- The slideshow media extends into all safe areas (full-bleed); all chrome stays inside
- Keyboard: the hairline message composer rises above the keyboard; the thread scrolls on pure black

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (pure black — never warmed)
- Surface 1: `#0D0D0D`
- Surface 2: `#161616`
- Divider: `#242424`
- Hairline (the only "border"/depth): `#2E2E2E`
- Text primary: `#FFFFFF`
- Text secondary: `#9A9A9A`
- Text tertiary / inactive: `#5E5E5E`
- Accent (the ONLY one): `#FFFFFF`
- White pressed: `#D8D8D8`
- On-photo white: `rgba(255,255,255,0.92)`
- On-photo dim: `rgba(255,255,255,0.6)`
- Photo scrim: `transparent → rgba(0,0,0,0.92)`
- Error (rare, reluctant): `#E5484D`

### Example Component Prompts
- "Build the Raya slideshow profile in SwiftUI: a full-bleed photo/video filling the entire screen (edge to edge, behind status bar). At the top safe-area: a row of segmented 2.5pt story bars, 16pt insets, 5pt gaps — played/active `#FFFFFF`, unplayed `rgba(255,255,255,0.3)`, each filling linearly over its slide's duration. Directly below the bars: a music ticker — a 13pt white vinyl/note glyph + '{Artist} — {Track}' in Inter 11pt w500 `rgba(255,255,255,0.85)` + a 4-bar white equalizer looping a height animation. A bottom scrim `transparent → rgba(0,0,0,0.92)` over the lower 62%. Caption ~96pt from bottom, 20pt insets: name in Inter 28pt w600 `#FFFFFF`; role in Inter 13pt w500 UPPERCASE `rgba(255,255,255,0.92)` +0.4px; bio in Inter 15pt `rgba(255,255,255,0.72)`; location with a 12pt pin glyph in Inter 13pt `rgba(255,255,255,0.6)`. Tap right/left thirds to scrub; center press-hold to pause."

- "Create the Raya action row: bottom-center, safe-area + 18pt, 56pt gaps. Skip — a 26pt `rgba(255,255,255,0.9)` X, no circle. Heart — a 60pt circle with a 1px `#FFFFFF` border and a white heart glyph (OUTLINE, never filled — the single focal action). Note — a 24pt `rgba(255,255,255,0.9)` message glyph, no circle. On Like: the heart fills white for 200ms, scales 1.0→1.1→1.0, then the next member's slideshow cross-fades in. No confetti, no color."

- "Render the Raya primary button: a fully-rounded pill (999pt), background `#FFFFFF` (the only filled element in the UI), label in Inter 14pt w600 `#000000` +0.3px, 15pt/30pt padding. Pressed: `#D8D8D8` + scale 0.99. One per screen. Secondary: transparent fill, 1px `#FFFFFF` border, `#FFFFFF` label."

- "Build the Raya music ticker: positioned under the story bars over the photo. A 13pt vinyl-record glyph (white, 2px stroke), '{Artist} — {Track}' in Inter 11pt w500 `rgba(255,255,255,0.85)`, and a trailing 4-bar equalizer (2px-wide white bars, 1px radius) looping a ~600ms height animation to imply playback. Tap (≥44pt) to expand the track. On track change, cross-fade the text over 180ms."

- "Create the Raya member chip: a pill (999pt), Inter 12pt w500, 8pt/16pt padding. Default: transparent fill, 1px `#2E2E2E` border, `#FFFFFF` text. Selected: solid `#FFFFFF` fill, `#000000` text — the inversion is the ONLY state change (no color). No shadow."

- "Build the Raya bottom tab bar (Messages/Profile screens only): 56pt + safe area, opaque `#000000` background (NO blur — true black is its own surface) with a 1px `#2E2E2E` top hairline. 3 tabs — Discover, Messages, Profile — 22pt fine-line icons (1.5px stroke, never filled), labels Inter 10pt w500 +0.4px. Active `#FFFFFF`, inactive `#5E5E5E`. No active pill — the white tint alone signals selection."

### Iteration Guide
1. Canvas is PURE black `#000000` — never warmed, never charcoal; the gallery void
2. There is exactly ONE accent: pure white `#FFFFFF` — no like-red, no gold, no green, ever
3. The member's photography/video is the ONLY color in the product — never tint, filter, or colored-frame it
4. The profile is a full-bleed SLIDESHOW paced by segmented white story bars, not a static card
5. Score it with the member's chosen song and show a small white music ticker + equalizer — this is the signature
6. Role lines are UPPERCASE letter-spaced Inter ("DIRECTOR · FILM & MUSIC") — the editorial-masthead tic
7. NO shadows — depth is a faint surface lift (`#0D0D0D` vs `#000000`) + a 1px `#2E2E2E` hairline
8. White fill is rationed to one CTA; the heart is an OUTLINE, never a fill
9. One neo-grotesque family for everything — the typographic mirror of the one-color discipline
10. Permanently dark, permanently restrained — the absence of decoration IS the brand
