# Design System Inspiration of Headspace (iOS)

## 1. Visual Theme & Atmosphere

Headspace is a hand-drawn morning — warm, slow, and humanly imperfect. Where Nike Run Club is a stadium and Strava is a stopwatch, Headspace is a pottery studio. The canvas is butter-cream (`#FFF7E7`) — never pure white, never gray — and the palette behaves like a sunset gradient: a marigold orange (`#FF6B35`), a butter yellow (`#FFD25C`), a moss sage (`#7E9F4B`), and a deep dusk-eggplant (`#2E1A47`) for surface contrast. Every screen looks like a children's book illustration of a meditation: spheres breathe, characters smile, gradients soften everything. Headspace is the most distinctive UI in the entire wellness category because it commits fully to its illustration style rather than relying on photography.

The animated meditation circles are the brand's signature visual — a sphere that breathes (scale 1.0 → 1.04 → 1.0 over 8 seconds, in time with the inhale/exhale guidance), tinted in a warm gradient (Marigold → Butter), pulsing as the meditation session progresses. On the Today screen, an "Aurora gradient" wash (Marigold → Butter → Coral) blankets the top third — it's the visual equivalent of waking up. The illustration set features round-headed characters in soft pastel hoodies, doing things like sitting cross-legged on hilltops, holding mugs, walking dogs. They appear on every empty state, every milestone reveal, every meditation completion screen — never as marketing pop-ups, always as gentle companions to the moment.

Typography is Apercu (a soft, slightly humanist sans by Colophon Foundry) at five weights. Apercu's lowercase 'a' has a curl that makes it feel like it was drawn rather than constructed — the perfect match for the illustration style. Headspace uses generous line-height (1.5 on body), title-case (not all-caps, ever), and a warm Ink Brown (`#2E1A47`) for text — never true black. The hierarchy is calming: 28pt for screen titles, 22pt for meditation titles, 18pt for body, 13pt for metadata. Numbers are not the star here — minutes meditated, streak count, all rendered tabular but never screaming.

The most signature screen is the in-meditation Play screen: butter-cream canvas, a breathing sphere in the center filling 60% of the screen, soft guidance text above it ("Take a deep breath in..."), and a thin progress arc at the very top. Below the sphere, a play/pause control. Nothing else. It's the meditative app pretending it isn't an app at all.

**Key Characteristics:**
- Butter-cream canvas (`#FFF7E7`) — warm, off-white, never pure
- Marigold Orange (`#FF6B35`) primary accent — Today CTA, primary buttons, meditation sphere gradient stop
- Butter Yellow (`#FFD25C`) secondary accent — selected pills, breath-cycle pacing chip
- Moss Sage (`#7E9F4B`) tertiary accent — Sleep tab, meditation streak chips
- Dusk Eggplant (`#2E1A47`) — primary text and the rare dark surface (sleep mode)
- Aurora gradient — Marigold → Butter → Coral wash on Today screen and meditation hero cards
- Breathing meditation sphere — gradient circle 60% of screen, scales 1.0 → 1.04 → 1.0 over 8s during inhale/exhale
- Illustration-heavy — round-headed characters in pastel hoodies on every empty state and milestone reveal
- Apercu typography at 5 weights with generous 1.5 line-height — feels like a journal
- Bottom tab bar: Today / Meditate / Sleep / Move / Profile — Marigold active, Sage on Sleep tab
- Card corners at 24pt — soft, comfortable, never aggressive
- Soft shadows (`rgba(46,26,71,0.08)`) tinted with the eggplant ink so they feel warm, not gray
- Audio scrubber is a soft sage track with a butter ball — never tech-blue

## 2. Color Palette & Roles

### Primary
- **Marigold Orange** (`#FF6B35`): Today CTA, primary buttons, "Continue" / "Start", meditation sphere gradient stop.
- **Marigold Pressed** (`#E55A2B`): Active/pressed state on Marigold CTAs.
- **Marigold Light** (`#FFB89E`): Background tints, illustration accents, Today-screen sky gradient stop.

### Aurora Gradient (signature wash)
A 3-stop linear gradient used on the Today hero and many full-bleed meditation cards.
- **Aurora Stop 1** (`#FFB89E`): Top (peachy dawn)
- **Aurora Stop 2** (`#FFD25C`): Middle (butter sun)
- **Aurora Stop 3** (`#FF6B35`): Bottom (marigold morning)

### Secondary (Mood Accents)
- **Butter Yellow** (`#FFD25C`): Selected pills, breath-cycle indicator, "feel happier" mood tag.
- **Sage Moss** (`#7E9F4B`): Sleep tab tint, meditation streak chips, ground accents in illustrations.
- **Sage Light** (`#B4C68F`): Backgrounds on Sleep cards, soft sage tint.
- **Coral Flush** (`#F4877E`): "Feel calm" mood tag, illustration cheeks.
- **Sky Lavender** (`#B7B0DC`): "Mindful Moment" tag, evening meditation accent.

### Sleep / Evening Mode (Dark)
Headspace's Sleep mode shifts the entire canvas to a deep dusk palette — gentle, warm, sleepy.
- **Dusk Canvas** (`#1A1430`): Primary Sleep / dark-mode canvas (warm purple-blue).
- **Dusk Surface 1** (`#2A2046`): Card backgrounds.
- **Dusk Surface 2** (`#3A2F5C`): Pressed states.
- **Dusk Divider** (`#3F3460`): Hairlines.

### Canvas, Surfaces & Dividers (Light / Default Mode)
- **Butter Cream** (`#FFF7E7`): Primary canvas everywhere except Sleep.
- **Off Cream** (`#FBF1DA`): Card backgrounds, search-field fill, settings rows.
- **Warm Gray** (`#E8DFC9`): Dividers, chip outlines.
- **Surface Light** (`#FFFFFF`): Used very sparingly — only inside text-input dialogs.

### Text
- **Ink Brown** (`#2E1A47`): Primary text — screen titles, meditation titles, body. The Dusk Eggplant doubles as the text color; it's warm and never harsh.
- **Ink Brown Soft** (`#594675`): Secondary text — metadata, "Hosted by Andy Puddicombe", session subtitles.
- **Ink Brown Mute** (`#8E7DA5`): Tertiary — placeholders, captions.
- **Inverted Cream** (`#FFF7E7`): Text on Sleep mode surfaces.

### Semantic
- **Success Sage** (`#7E9F4B`): Streak maintained, session completed.
- **Warning Amber** (`#FF9F4A`): Subscription expiring.
- **Error Coral** (`#E04646`): Failed sync, payment failed — never a hard red, just a muted coral.

### Streak & Milestone Colors
- **Streak Marigold** (`#FF6B35`): Active streak ring.
- **Milestone Gold** (`#E5B85C`): "30 days mindful" — a warm gold, not a metallic yellow.
- **Achievement Coral** (`#F4877E`): Special edition badges.

## 3. Typography Rules

### Font Family
- **Primary**: `Apercu` (Colophon Foundry, 2010) — Headspace's house face since their 2017 rebrand
- **Weights available**: Light (300), Regular (400), Medium (500), Bold (700), Black (900)
- **Fallback stack**: `'Apercu', 'Apercu Pro', 'Avenir Next', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Arial, sans-serif`
- **Web/marketing Google Fonts substitute**: `Nunito` (soft humanist sans, similar curl on the 'a'); also `Manrope` works as a slightly cooler stand-in
- **Body fallback**: SF Pro Text is acceptable for the iOS app where Apercu isn't licensed

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Today Greeting Hero | Apercu | 32pt | 700 (Bold) | 1.2 | -0.3pt | "Good morning, Sam" — first thing on Today |
| Screen Title | Apercu | 28pt | 700 | 1.2 | -0.3pt | "Meditate", "Sleep", "Move" |
| Meditation Title | Apercu | 22pt | 700 | 1.3 | -0.2pt | "Stress Release" on a session card |
| Section Header | Apercu | 18pt | 700 | 1.3 | -0.1pt | "Featured this week", "Quick meditations" |
| Card Title | Apercu | 17pt | 600 (Medium) | 1.3 | -0.1pt | Smaller card titles, list items |
| Body | Apercu | 16pt | 400 (Regular) | 1.5 | 0pt | Description copy, journal entries — generous line-height |
| Body Bold | Apercu | 16pt | 600 | 1.5 | 0pt | Inline emphasis |
| Meditation Guidance | Apercu | 22pt | 400 | 1.4 | 0pt | "Take a deep breath in..." on the play screen — large, breathing |
| Metadata | Apercu | 13pt | 400 | 1.3 | 0pt | "Andy Puddicombe · 10 min", "20:32 remaining" |
| Tag Pill | Apercu | 13pt | 600 | 1.0 | 0.2pt | "Calm", "Stress", "Sleep" — title-case |
| Button (Primary) | Apercu | 17pt | 600 | 1.0 | 0pt | "Start", "Continue", "Save" — title-case |
| Tab Label | Apercu | 11pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Time Display | Apercu | 56pt | 700 | 1.0 | -0.5pt | "10:00" countdown on play screen |
| Streak Number | Apercu | 22pt | 700 | 1.0 | 0pt | "12" days in a row — tabular |
| Caption | Apercu | 11pt | 400 | 1.3 | 0pt | Footnotes, "tap to learn more" |

### Principles
- **Title case, never all caps**: Apercu is a gentle face — UPPERCASE on Apercu reads as shouty and breaks the meditative tone
- **Weights concentrated at 400 / 600 / 700**: Regular for body, Medium for card titles and buttons, Bold for screen titles and section headers; Light and Black appear only on the rare illustration overlay
- **Generous line-height (1.5 on body, 1.4 on guidance)** — text feels paced, like breath
- **Ink Brown over true black**: body text is `#2E1A47` (the Dusk Eggplant) — warm purple-brown, never harsh black
- **No letter-spacing tricks**: title case at 0 tracking is the default; very tight tracking (-0.3pt) is reserved for the 32pt greeting hero
- **`monospacedDigit()` on the countdown timer** — minutes:seconds need to align as the timer ticks
- **Dynamic Type respected on body, titles, and guidance**; FIXED on tag pills, tab labels, streak numbers (small, layout-sensitive)

## 4. Component Stylings

### Breathing Meditation Sphere (the hero component)

**Design**
- A circle in the center of the play screen
- Diameter: 60% of screen width (≈234pt on iPhone 15)
- Fill: radial gradient — `#FFB89E` 0% (highlight, top-left) → `#FF6B35` 70% → `#E55A2B` 100% (darkest, bottom-right)
- Border: 1pt `rgba(46,26,71,0.08)` very soft to give the sphere a planet-like edge

**Breath Animation**
- Continuous loop while in a breathing exercise
- Inhale: 4-second scale from 1.0 → 1.04 with `.easeInOut`
- Hold: 2-second hold at 1.04 with no animation
- Exhale: 4-second scale from 1.04 → 0.96 with `.easeInOut`
- Hold: 2-second hold at 0.96
- The total breath cycle is 12 seconds; the sphere is the visual metronome

**Particle Halo (optional, during sleep meditations)**
- 12 small `#FFD25C` butter-yellow dots orbiting the sphere at 200pt radius, 360° in 60 seconds, each at 30° spacing
- Each particle is 6pt with 50% opacity and slow fade in/out

### Today Hero (the Aurora gradient screen)

**Layout**
- Top third of the Today screen: full-bleed Aurora gradient (`#FFB89E` → `#FFD25C` → `#FF6B35`)
- Greeting: "Good morning, Sam" in Apercu 32pt Bold Ink Brown, top-left with 24pt inset and 16pt below safe area
- Subtitle: "Today's vibe: Mindful Moment" in Apercu 16pt Medium Ink Brown @ 80%, below greeting
- Featured meditation hero card: full-width minus 24pt margins, 200pt tall, 24pt corner radius, with the breathing sphere on the left and "Start your day with peace" copy on the right
- "Start" pill CTA: Marigold background, white text, full-width inside the card

**Below the Aurora wash**: butter-cream canvas resumes, with section "Quick meditations" horizontal scroll cards.

### Meditation Session Card

**Format**
- Width: full content width minus 24pt margins (or 280pt fixed in horizontal scroll)
- Height: 200pt
- Background: full-bleed illustration (a character meditating on a hilltop, sitting cross-legged, walking the dog) with `rgba(46,26,71,0.0 → 0.6)` gradient overlay bottom-up
- Corner radius: 24pt
- Bottom-aligned content:
  - Tag pill: "Stress" / "Sleep" / "Focus" in 13pt Semibold white on a `rgba(255,255,255,0.25)` glass background, full-pill 500pt
  - Title: "Stress Release" in Apercu 22pt Bold white
  - Subtitle: "Andy Puddicombe · 10 min" in Apercu 13pt Regular white @ 85%

**Shadow**: `rgba(46,26,71,0.08) 0 4px 16px` — warm, soft, tinted with the ink color

### Quick Action Tile (Today screen Quick Meditations row)

**Format**
- Width: 140pt fixed, in horizontal scroll
- Height: 180pt
- Background: solid soft color from the palette — Butter Yellow / Sage Light / Coral Flush / Sky Lavender / Marigold Light
- Corner radius: 20pt
- Content stack: 64pt illustration character at the top (centered), title "3-min Reset" in Apercu 17pt Medium Ink Brown, subtitle "Quick calm" in Apercu 13pt Regular Ink Brown @ 70%
- 16pt content inset all around
- Tap: scale 0.98, haptic `.impactOccurred(.soft)`

### Player Controls (in-meditation play screen)

**Layout**
- Bottom of screen, below the breathing sphere
- Horizontal row: skip-back 15s / play-pause (large) / skip-forward 15s — centered
- Play/Pause: 72pt × 72pt circular, Marigold `#FF6B35` background, white `play.fill` / `pause.fill` 28pt glyph centered
- Skip buttons: 48pt × 48pt circular, Off-Cream `#FBF1DA` background, Ink Brown `gobackward.15` / `goforward.15` 22pt glyph centered

**Below the controls**: a thin progress arc on the very top of the screen rendering the elapsed/total minutes. Sage `#7E9F4B` filled, Warm Gray `#E8DFC9` track, 2pt stroke, rounded caps.

### Buttons

**Primary Pill CTA ("Start", "Continue", "Save")**
- Background: `#FF6B35` Marigold
- Text: `#FFFFFF`, Apercu 17pt Medium (NOT Bold — Headspace stays gentle)
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 28pt (full pill on a 56pt button)
- Height: 56pt
- Pressed: background `#E55A2B`, scale 0.98
- Shadow: `rgba(255,107,53,0.25) 0 6px 16px` — a soft warm glow
- Haptic: `.impactOccurred(.soft)` — Headspace's haptics are always soft, never heavy

**Secondary Outline Button**
- Background: transparent
- Border: 1.5pt `#2E1A47` Ink Brown
- Text: `#2E1A47`, Apercu 17pt Medium
- Corner radius: 28pt full pill
- Padding: 12pt vertical, 26pt horizontal

**Soft Tag Pill (selected mood / category)**
- Background: `#FFD25C` Butter Yellow when selected; `#FBF1DA` when unselected
- Text: `#2E1A47` Apercu 13pt Semibold title-case
- Padding: 6pt vertical, 14pt horizontal
- Corner radius: 500pt full pill
- Selected has a subtle 1pt darker inner glow

**Text Link**
- "See all", "Tap to continue" — Apercu 13pt Semibold `#FF6B35` Marigold, no underline
- 44pt hit target

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFF7E7` Butter Cream with 0.5pt `#E8DFC9` top hairline; on Sleep mode, switches to `#1A1430` Dusk
- Tabs (5): Today / Meditate / Sleep / Move / Profile
- Icon: 24pt SF Symbol — outlined inactive, filled active
- Active color: Marigold `#FF6B35` on Today/Meditate/Move/Profile; Sage `#7E9F4B` on Sleep (the only tab with its own tint)
- Inactive: Ink Brown Mute `#8E7DA5`
- Labels: Apercu 11pt Medium, mixed case
- Tap haptic: `.selectionChanged()`

**Top App Bar**
- Height: 56pt + safe area
- Background: matches the screen — butter cream or sleep dusk
- Title: Apercu 28pt Bold Ink Brown, left-aligned with 24pt inset
- Right: profile avatar (28pt circle) + optional settings glyph at 22pt Ink Brown

### Input Fields

**Search Field (Library search)**
- Background: `#FBF1DA` Off Cream
- Border: 0
- Corner radius: 20pt
- Height: 44pt
- Leading: 16pt `magnifyingglass` Ink Brown Soft
- Text: Apercu 16pt Regular Ink Brown
- Placeholder: Ink Brown Mute

**Journal Text Area**
- Background: `#FFF7E7` Butter Cream (matches canvas)
- Border: 1pt `#E8DFC9` Warm Gray, dashed (gives the journal entry a hand-drawn quality)
- Corner radius: 20pt
- Padding: 16pt all around
- Text: Apercu 16pt Regular Ink Brown, 1.5 line height

### Distinctive Components

**Streak Ring (Profile)**
- 120pt circular ring with Marigold `#FF6B35` fill animating from 0 to (currentStreak / 30 days) progress
- Track: `#FBF1DA` Off Cream, 8pt stroke
- Fill: Marigold, 8pt stroke, rounded caps, animates clockwise from 12 o'clock
- Inside the ring: streak number in Apercu 56pt Bold tabular Ink Brown + "days" in Apercu 13pt Regular Ink Brown Soft, stacked center

**Illustration Companion**
- A round-headed character in a pastel hoodie — sitting cross-legged, walking, holding a mug
- Appears on every empty state, every milestone reveal, and the bottom-right corner of the Today hero (subtle, like a thumbnail)
- Sizes: 80pt for inline contextual companions, 240pt for hero milestone reveals, 64pt for quick-action tile thumbnails
- Style: thick rounded line work, pastel fills, no shading — pure illustration style, never photorealistic

**Mood Tag Row (Today screen, "How are you feeling?")**
- Horizontal row of 5 selectable mood pills with emoji characters and labels: Happy / Calm / Sad / Anxious / Excited
- Each pill is 80pt wide × 88pt tall, vertically stacked icon + label
- Unselected: `#FBF1DA` background
- Selected: `#FFD25C` Butter Yellow background with the mood emoji enlarged
- Tap haptic: `.selectionChanged()`

**Sleep Story Card (Sleep tab)**
- Full-width minus 24pt margins, 240pt tall
- Background: nighttime illustration (a forest at twilight, an ocean under stars, a cabin in snow)
- Overlay: `rgba(26,20,48,0.4)` Dusk Canvas tint
- Title: "The Falling Snow" in Apercu 22pt Bold inverted cream
- Narrator: "Read by Cillian Murphy" in Apercu 13pt Regular inverted cream @ 80%
- Duration: "45 min" in Apercu 11pt Regular inverted cream @ 70%
- Corner radius: 24pt

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 24pt on most screens — generous, gives content room to breathe
- Section vertical gap: 32pt between major sections; 48pt above hero CTAs on play screens

### Grid & Container
- Content width: full minus 24pt margins (wider than other apps because Headspace prefers air)
- Meditation cards: full-width on Today and Library; 280pt fixed in horizontal scroll Quick Meditations rows
- Mood tags: 5-column equal-width row
- Library category grid: 2-col stacked grid with 16pt gap

### Whitespace Philosophy
- **Generous breathing room everywhere**: 24pt margins, 1.5 line-height, 32pt section gaps — the visual equivalent of slow inhalation
- **No tight stacking**: cards never abut; always 16pt vertical gap between cards
- **Hero illustration breathes**: the breathing sphere on the play screen has 80pt of clearance above and below — it must feel uncrowded

### Border Radius Scale
- Standard (12pt): tag pills (small), small chips
- Medium (16pt): journal text area, settings panels
- Soft (20pt): search field, quick-action tile
- Comfortable (24pt): meditation cards, sleep cards, modal sheets — Headspace's signature radius
- Pill (500pt): action buttons, tag pills, selected mood pills
- Circle (50%): avatars, play/pause button, breathing sphere

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, journal entries, body canvas |
| Card (Level 1) | `rgba(46,26,71,0.06) 0 2px 8px` | Quick-action tiles, secondary cards |
| Floating (Level 2) | `rgba(46,26,71,0.08) 0 4px 16px` | Meditation cards, sleep story cards |
| Pop (Level 3) | `rgba(255,107,53,0.25) 0 6px 16px` Marigold-tinted | Primary CTA buttons — a warm glow, not a neutral shadow |
| Sheet (Level 4) | `rgba(46,26,71,0.12) 0 -8px 32px` | Modal sheets, "Add to favorites" bottom sheet |

**Shadow Philosophy**: Headspace shadows are always warm — tinted with the Ink Brown (`#2E1A47`) instead of true black, so they read as soft and friendly. Primary CTAs use a Marigold-tinted shadow (a sunrise glow). Shadows are never aggressive — `0.06` to `0.12` opacity max.

### Motion
- **Breathing sphere**: continuous 12s cycle — 4s inhale (1.0 → 1.04), 2s hold, 4s exhale (1.04 → 0.96), 2s hold; pure `.easeInOut`, no spring
- **Primary CTA tap**: scale 0.98 + `.impactOccurred(.soft)` haptic + warm glow shadow brightens momentarily
- **Mood tag select**: scale 1.0 → 1.1 → 1.0 over 300ms spring (damping 0.7) + Butter Yellow background fill
- **Streak ring animate**: 1.2s ease-out fill from 0 to currentProgress on first view + soft success haptic
- **Card tap**: scale 0.99 (very subtle), 200ms ease, transition to detail
- **Tab switch**: `.selectionChanged()` haptic + 200ms cross-fade between icons (outlined → filled)
- **Meditation completion**: illustration companion fades in at 0.5x → 1.0x spring + soft success haptic + "You did it" copy in Apercu 28pt Bold
- **Sleep mode transition**: canvas color cross-fades from Butter Cream `#FFF7E7` to Dusk `#1A1430` over 600ms when entering the Sleep tab — visceral, like dusk falling

## 7. Do's and Don'ts

### Do
- Use Butter Cream `#FFF7E7` for the canvas — never pure white, never cool gray
- Use Marigold `#FF6B35` as the primary action color — Start, Continue, Save
- Apply the Aurora gradient (Marigold → Butter → Coral) on Today hero and meditation full-bleed cards
- Render the breathing sphere as a radial gradient (`#FFB89E` highlight → `#FF6B35` mid → `#E55A2B` shadow) with continuous breath animation
- Use Apercu (or Nunito as fallback) — soft humanist sans, never grotesque
- Use title case on every label — never UPPERCASE; UPPERCASE feels shouty
- Use Ink Brown `#2E1A47` for body text — warm purple-brown, never harsh black
- Use illustration companions on every empty state and milestone reveal — characters are the brand
- Use generous spacing — 24pt margins, 1.5 body line-height — pace it like breath
- Use 24pt corner radius on meditation cards — Headspace's signature soft corner
- Use warm Ink-tinted shadows (`rgba(46,26,71,0.08)`) — never true-black shadows
- Use Marigold-tinted glow under primary CTAs — a sunrise, not a drop shadow
- Use `.impactOccurred(.soft)` haptics — Headspace's haptic vocabulary is always soft
- Tint the Sleep tab Sage `#7E9F4B` — the only tab with its own color; Sleep is its own world

### Don't
- Don't use pure white as the canvas — Headspace is warm-cream by brand
- Don't use harsh black `#000000` for text — Ink Brown `#2E1A47` is the value
- Don't use UPPERCASE on Apercu — title case only, never all-caps
- Don't replace illustration companions with photography — Headspace is fully illustrated
- Don't use cool blues or grotesque sans-serif typography — the brand is warm and humanist
- Don't use heavy haptics — Headspace's haptic vocabulary is all soft and selection
- Don't use a true-black drop shadow — shadows are tinted with Ink Brown for warmth
- Don't animate the breathing sphere with a spring — pure ease-in-out is correct (springs feel digital)
- Don't render the meditation sphere flat — the radial gradient is what makes it feel like a sun
- Don't pack content tight — Headspace breathes; 24pt margins minimum
- Don't use Marigold as the Sleep mode primary — Sage is the Sleep accent
- Don't dim the canvas to dark gray on Sleep mode — Dusk `#1A1430` is the value (warm purple-blue)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Breathing sphere shrinks to 200pt; greeting at 28pt |
| iPhone 13/14/15 | 390pt | Standard 234pt sphere, 32pt greeting |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected — top app bar sits below it |
| iPhone 15/16 Pro Max | 430pt | Larger 260pt sphere, 36pt greeting |
| Apple Watch (companion) | 184-220pt | Breathing sphere only, no other UI |
| iPad | 768pt+ | Library uses 3-col grid, meditation play screen centers the sphere in a 480pt frame |

### Dynamic Type
- Greeting, screen titles, body, guidance copy: full scale
- Meditation timer countdown (56pt): scales up to xLarge then clamps
- Tab labels, tag pills, streak number, mood tag labels: FIXED (layout-sensitive)

### Orientation
- Portrait-locked everywhere — Headspace is a single-hand, single-orientation app
- Sleep stories may allow landscape if user holds phone sideways while lying down (rare)

### Touch Targets
- Mood tag pill: 80pt × 88pt visual, full hit area
- Play/Pause button: 72pt visual, 80pt hit area
- Tab icons: 24pt visual, 56pt row hit area
- Tag pill: 44pt height visible, full row hit

### Safe Area Handling
- Top: safe area honored — greeting starts at safe-area top + 16pt
- Bottom: tab bar respects home indicator; play screen controls have 32pt clearance above home indicator
- Sides: 24pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFF7E7` (Butter Cream)
- Off Cream Surface: `#FBF1DA`
- Warm Gray Divider: `#E8DFC9`
- Marigold (primary): `#FF6B35`
- Marigold Pressed: `#E55A2B`
- Marigold Light: `#FFB89E`
- Butter Yellow: `#FFD25C`
- Sage Moss: `#7E9F4B`
- Sage Light: `#B4C68F`
- Coral Flush: `#F4877E`
- Sky Lavender: `#B7B0DC`
- Ink Brown (text): `#2E1A47`
- Ink Brown Soft: `#594675`
- Ink Brown Mute: `#8E7DA5`
- Dusk Canvas (sleep): `#1A1430`
- Dusk Surface 1: `#2A2046`
- Aurora gradient: `#FFB89E` → `#FFD25C` → `#FF6B35`

### Example Component Prompts
- "Build the Headspace breathing sphere: 234pt diameter circle at the center of the play screen on a Butter Cream `#FFF7E7` canvas. Fill is a radial gradient from `#FFB89E` 0% (top-left highlight) to `#FF6B35` 70% to `#E55A2B` 100% (bottom-right shadow). 1pt outline at `rgba(46,26,71,0.08)` for a soft edge. Apply a continuous breath animation: 4s inhale scaling 1.0 → 1.04 with `.easeInOut`, 2s hold, 4s exhale scaling 1.04 → 0.96, 2s hold — total 12s cycle, looping. Above the sphere: 'Take a deep breath in...' in Apercu 22pt Regular Ink Brown `#2E1A47`. Below the sphere: countdown timer '10:00' in Apercu 56pt Bold tabular Ink Brown."
- "Render the Today hero: top third of the screen full-bleed with a 3-stop linear gradient — `#FFB89E` 0%, `#FFD25C` 50%, `#FF6B35` 100% (the Aurora wash). Greeting 'Good morning, Sam' in Apercu 32pt Bold `#2E1A47` Ink Brown at top-left with 24pt inset and 16pt below safe area. Subtitle 'Today's vibe: Mindful Moment' in Apercu 16pt Medium Ink Brown @ 80%. Below the wash on butter-cream canvas: a featured meditation card (full-width minus 24pt margins, 200pt tall, 24pt corner radius) with the breathing sphere illustration on the left and 'Start your day with peace' copy + Marigold 'Start' pill CTA on the right."
- "Build the Headspace primary CTA: a 56pt-tall Marigold `#FF6B35` pill (corner radius 28pt full pill), full-width minus 24pt margins. Label 'Start' in Apercu 17pt Medium (NOT Bold — Headspace stays gentle) white centered. Shadow `rgba(255,107,53,0.25) 0 6px 16px` — a warm sunrise glow. On tap: scale 0.98 + haptic `.impactOccurred(.soft)`. Pressed state turns the background `#E55A2B`."
- "Design the streak ring on the Profile screen: 120pt circular ring, Off Cream `#FBF1DA` track at 8pt stroke, Marigold `#FF6B35` fill at 8pt stroke with rounded caps. Fill represents the user's current streak as a fraction of 30 days, animating clockwise from -90° on first view over 1.2s with `.easeOut`. Inside the ring: streak number '12' in Apercu 56pt Bold tabular `#2E1A47`, then 'days' below it in Apercu 13pt Regular `#594675` Ink Brown Soft. Soft success haptic on reveal."
- "Render a meditation session card: full-width minus 24pt margins, 200pt tall, 24pt corner radius. Background is a full-bleed illustration of a round-headed character meditating cross-legged on a hill, in soft pastel colors. Apply a `linear-gradient(transparent → rgba(46,26,71,0.6))` overlay bottom-up. Bottom-aligned content: a 'Stress' tag pill in Apercu 13pt Semibold white on `rgba(255,255,255,0.25)` glass background (full pill 500pt corner). Below the pill: 'Stress Release' title in Apercu 22pt Bold white. Below the title: 'Andy Puddicombe · 10 min' in Apercu 13pt Regular white @ 85%. Shadow `rgba(46,26,71,0.08) 0 4px 16px` — warm and soft."

### Iteration Guide
1. Canvas is `#FFF7E7` Butter Cream — warm, off-white, never pure
2. Marigold `#FF6B35` is the primary action color — Today CTA, Start, Continue
3. Aurora gradient (`#FFB89E` → `#FFD25C` → `#FF6B35`) is the brand wash — Today hero, meditation hero cards
4. Body text is Ink Brown `#2E1A47` — warm purple-brown, never harsh black
5. The breathing sphere is the hero — radial gradient circle with continuous 12s breath animation
6. Apercu typography (Nunito Google fallback) at weights 400/500/600/700 — title case only, never UPPERCASE
7. Generous spacing: 24pt margins, 1.5 line-height, 32pt section gaps — pace it like breath
8. 24pt corner radius on meditation cards — Headspace's signature soft corner
9. Soft warm shadows tinted with Ink Brown (`rgba(46,26,71,0.08)`) — never true-black shadows
10. Soft haptics everywhere — `.impactOccurred(.soft)` and `.selectionChanged()`, never heavy
