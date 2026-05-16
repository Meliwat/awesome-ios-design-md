# Design System Inspiration of Shazam (iOS)

## 1. Visual Theme & Atmosphere

Shazam's iOS app is a single-purpose instrument, and its design is ruthlessly devoted to that one purpose: press the button, name the song. The canvas is not a flat color but a deep radial gradient — Shazam Blue (`#0088FF`) blooming from a brighter `#0050FF` core out into a near-black space tint (`#08090E`) at the edges. The effect is a glowing well of blue with a single object at the center of gravity: the Shazam button. There is no feed, no tab bar, no navigation chrome competing for attention on the main screen. The entire interface is the gradient, the button, and the concentric pulse rings that radiate from it while listening.

The accent system is monochromatic blue with light text. Shazam Blue (`#0088FF`) is the brand, the button, the active state, and the link color; a deeper pressed blue (`#006FE0`) handles tap-down. Surfaces are not opaque panels but translucent glass — `rgba(255,255,255,0.08)` cards floating over the gradient, with `rgba(255,255,255,0.12)` hairline dividers. White (`#FFFFFF`) is primary text; a cool periwinkle (`#B8C4FF`) is secondary text, chosen because it harmonizes with the blue gradient rather than fighting it. The whole app feels like a piece of consumer hardware — a single glowing tap-target you point at the world.

Typography is Montserrat — a geometric sans with a confident, rounded warmth (SF Pro is the production fallback). Weights stay at 500 / 700. The type is sparse: there are no dense lists on the hero screen, so type appears in punchy, well-spaced moments — the "Tap to Shazam" prompt under the button, the result card's track title and artist, and the recent-shazams list in the bottom sheet. The most expressive typographic moment is the result reveal: the matched track's title animates in at 22-28pt bold over its album art, with an "Open in Apple Music" affordance directly beneath.

**Key Characteristics:**
- Radial blue gradient canvas (`#0050FF` → `#0088FF` over `#08090E`) — a glowing well, not a flat color
- A single giant central Shazam button with concentric pulse rings — the entire UI's center of gravity
- **No tab bar** — one hero screen plus a draggable bottom sheet for library/history
- Translucent glass surfaces (`rgba(255,255,255,0.08)`) floating over the gradient
- Pulsing tap-to-listen interaction — rings expand outward while the mic is open
- Result card: album art + title + artist + "Open in Apple Music"
- Recent-shazams list in the bottom sheet
- Montserrat typography (SF Pro fallback), weights 500/700
- Periwinkle secondary text (`#B8C4FF`) tuned to the blue gradient

## 2. Color Palette & Roles

### Primary
- **Shazam Blue** (`#0088FF`): The brand, the button core, active states, links, the "Open in Apple Music" affordance accent.
- **Bright Core** (`#0050FF`): The brighter center of the radial gradient, behind the button.
- **Blue Pressed** (`#006FE0`): Tap-down state for the Shazam button and blue controls.

### Canvas & Gradient
- **Space Tint** (`#08090E`): The near-black edge of the radial gradient (the "void" the blue blooms from).
- **Radial Canvas**: `radial-gradient(circle at 50% 42%, #0088FF 0%, #0050FF 30%, #08090E 100%)` — the signature hero background.
- **Gradient Core** (`#0050FF`): Bright center directly behind the button.

### Surfaces (Translucent Glass)
- **Surface Glass** (`rgba(255,255,255,0.08)`): Cards, the result card, sheet rows — floating over the gradient.
- **Surface Glass Strong** (`rgba(255,255,255,0.14)`): Pressed glass, the bottom-sheet background.
- **Divider** (`rgba(255,255,255,0.12)`): Hairline dividers between sheet rows.

### Text
- **Text Primary** (`#FFFFFF`): Track titles, the "Tap to Shazam" prompt, primary UI text.
- **Text Secondary** (`#B8C4FF`): Artist names, metadata, sheet subtitles — periwinkle, tuned to the blue.
- **Text Tertiary** (`rgba(184,196,255,0.55)`): Disabled state, very low-emphasis labels.

### Semantic
- **Pulse Ring** (`rgba(255,255,255,0.22)` → fades to `0`): The concentric rings expanding from the listening button.
- **Apple Music Pink** (`#FA243C`): The Apple Music logo accent on the "Open in Apple Music" button (brand-accurate, used only there).
- **Listening Glow** (`rgba(0,136,255,0.5)`): The amplified glow around the button while the mic is open.
- **Error Red** (`#FF453A`): "No match found" / connectivity errors.

### Light Mode
Shazam's iOS hero experience is dark by design — the radial blue gradient over a near-black space tint is the brand identity and the optimal stage for the glowing button. Result cards and sheets remain on the translucent-glass-over-gradient treatment regardless of system appearance.

## 3. Typography Rules

### Font Family
- **Primary**: `Montserrat` — geometric sans with rounded warmth (Shazam's brand voice)
- **Production Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Proportional figures (no dense tabular data on the hero — counts are rare)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Result Title (Large) | Montserrat | 28pt | 700 | 1.15 | -0.3pt | Matched track title on full result reveal |
| Result Title | Montserrat | 22pt | 700 | 1.2 | -0.2pt | Track title on the result card |
| Section Header | Montserrat | 20pt | 700 | 1.2 | -0.2pt | "Your Shazams", sheet section title |
| Tap Prompt | Montserrat | 18pt | 700 | 1.2 | 0.2pt | "Tap to Shazam" under the button |
| Card Title | Montserrat | 16pt | 700 | 1.3 | 0pt | Recent-shazam row title |
| Artist / Subtitle | Montserrat | 14pt | 500 | 1.3 | 0pt | Artist name on result + rows |
| Body | Montserrat | 15pt | 500 | 1.45 | 0pt | Lyrics snippet, descriptions |
| Meta / Time | Montserrat | 13pt | 500 | 1.3 | 0pt | "2h ago", "Yesterday" |
| Label (UPPER) | Montserrat | 11pt | 700 | 1.2 | 1.0pt | "NOW PLAYING", section eyebrow |
| Button (Primary) | Montserrat | 16pt | 700 | 1.0 | 0.3pt | "Open in Apple Music", primary CTAs |
| Button (Secondary) | Montserrat | 14pt | 700 | 1.0 | 0.2pt | Glass secondary actions |
| Listening Label | Montserrat | 16pt | 700 | 1.2 | 0.2pt | "Listening for music…" while mic is open |

### Principles
- **Only two weights — 500 / 700**: Medium for body/secondary, Bold for everything that matters
- **Generous spacing, never dense**: The hero has one or two type moments — they get room to breathe
- **All-caps eyebrows with +1.0pt tracking** for the technical "NOW PLAYING" voice
- **Negative tracking only on large titles** (-0.2 to -0.3pt); prompts use slightly positive tracking
- **Dynamic Type respected on titles, artists, body**; the tap prompt and listening label stay fixed (centered layout)

## 4. Component Stylings

### Buttons

**The Shazam Button (Primary, Signature)**
- Shape: Circle, diameter 132pt (the dominant object on the hero screen)
- Background: a subtle white-to-glass radial — `radial-gradient(circle at 38% 32%, #FFFFFF 0%, #E8F0FF 60%, #CFE0FF 100%)` with a thin `rgba(255,255,255,0.4)` ring
- Glyph: the Shazam mark (a stylized "S" wave) in `#0088FF`, ~52pt
- Idle: a slow ~2.4s breathing pulse (scale 1.0 → 1.04 → 1.0)
- Pressed: scale 0.94, glyph tint `#006FE0`, with `.impactOccurred(.heavy)` haptic
- Listening: button stays put while concentric rings emit outward and a `rgba(0,136,255,0.5)` glow amplifies

**Open in Apple Music (Result CTA)**
- Background: `#FFFFFF`
- Text: `#08090E` with the Apple Music glyph in `#FA243C` leading
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 500pt (full pill)
- Font: Montserrat 16pt weight 700, +0.3pt tracking
- Pressed: `#E8F0FF`, scale 0.97

**Glass Secondary (Share / Add / Lyrics)**
- Background: `rgba(255,255,255,0.08)`
- Text: `#FFFFFF`
- Border: 1pt `rgba(255,255,255,0.16)`
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 500pt
- Font: Montserrat 14pt weight 700
- Pressed: background `rgba(255,255,255,0.14)`

**Icon Actions (Share, More, Library, Settings)**
- Size: 22pt glyph, 44pt hit area
- Default: `#FFFFFF` at 85% opacity
- Pressed: `#FFFFFF` full
- Top-bar icons (settings, library) sit at 24pt over the gradient

**Text Button ("See all", "View library")**
- Font: Montserrat 14pt weight 700
- Color: `#B8C4FF`
- No underline, 44pt hit area

### Cards & Containers

**Result Card**
- Surface: `rgba(255,255,255,0.08)` glass, 20pt corner radius, 1pt `rgba(255,255,255,0.14)` border
- Layout: 88pt × 88pt album art (12pt radius) leading → stacked title (22pt 700) + artist (14pt 500 `#B8C4FF`)
- Below: a full-width "Open in Apple Music" pill + a row of glass secondary actions
- Appears via a spring rise from the button position

**Recent-Shazam Row (Bottom Sheet)**
- Height: 68pt
- Layout: 52pt × 52pt album art (10pt radius) → stacked title (16pt 700) + artist (14pt 500 `#B8C4FF`) → trailing time (13pt 500 `#B8C4FF`) + 20pt chevron
- Background: transparent over the sheet glass; pressed: `rgba(255,255,255,0.06)`
- Divider: 1pt `rgba(255,255,255,0.12)` between rows

**Bottom Sheet (Library / History)**
- Background: `rgba(255,255,255,0.14)` with a heavy backdrop blur (40) over the gradient
- Top corner radius: 24pt; a 36pt × 4pt grab handle (`rgba(255,255,255,0.4)`) centered at the top
- Detents: peek (shows the grab handle + 1 row), half, full
- Drag up reveals "Your Shazams" — the chronological list of matched tracks

**Now-Playing Mini Strip (when audio plays)**
- Sits at the bottom above the sheet handle, full width, 56pt tall
- Surface: `rgba(255,255,255,0.10)` glass
- 40pt art (8pt radius) + title 14pt 700 + artist 12pt 500 `#B8C4FF` + play/pause 20pt

### Navigation

**No Tab Bar (Signature Absence)**
- Shazam's hero screen has *no* bottom tab bar — this is a deliberate, defining choice
- Navigation is: the hero button (center), a top-left settings/profile icon, a top-right library icon, and the upward-draggable bottom sheet for history
- This single-purpose layout is core to the brand — do not add tabs

**Top Bar (over the gradient)**
- Height: 44pt + safe area, fully transparent (the gradient shows through)
- Left: profile/settings icon (26pt, `#FFFFFF`)
- Right: library/history icon (26pt, `#FFFFFF`)
- No title — the button is the screen's subject

**Result Screen Nav**
- A back/close affordance (28pt `xmark` in a glass circle) top-left returns to the hero
- The result screen retains the gradient with the card floating over it

### Input Fields

**Search (within the library sheet)**
- Background: `rgba(255,255,255,0.10)` glass
- Height: 44pt
- Corner radius: 500pt (full pill)
- Leading SF Symbol `magnifyingglass`, 18pt, `#B8C4FF`
- Placeholder: "Search your Shazams", 16pt 500 `#B8C4FF`
- Focus: 1pt `#0088FF` border, cursor `#0088FF`
- Text in field: `#FFFFFF`

**Filter Chip (library)**
- Background: `rgba(255,255,255,0.08)` default / `#0088FF` selected
- Text: `#FFFFFF` (both states)
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 500pt (full pill)
- Font: Montserrat 14pt weight 700
- Examples: "All", "Apple Music", "Concerts"

### Distinctive Components

**The Pulsing Tap-to-Listen Button + Concentric Rings**
- The hero button is wrapped by 3 concentric rings
- Idle: rings are invisible; the button breathes (scale 1.0 → 1.04 → 1.0 over 2.4s)
- On tap (listening): rings emit outward continuously — each ring starts at the button radius, scales to ~2.6×, and fades opacity 0.22 → 0 over 1.8s, staggered by 0.6s so a new ring launches as the previous fades
- A `rgba(0,136,255,0.5)` glow blooms around the button while the mic is open
- The prompt text swaps "Tap to Shazam" → "Listening for music…"
- This expanding-ripple system is the single most recognizable Shazam interaction

**Result Reveal Transition**
- On match, the rings collapse, the button shrinks toward the top, and the result card springs up from the button's position (scale + opacity, 0.45s spring, damping 0.8)
- The matched album art is the visual anchor of the rise

**Recent-Shazams Sheet Drag**
- The bottom sheet uses native-feel detents; dragging up over the gradient reveals the chronological list
- Each row's album art is full-color (the only saturated content over the monochrome-blue stage)

**Auto-Shazam / Background Pulse (Concept)**
- When Auto-Shazam is on, a subtle persistent ring breathes at the top of the screen as an always-listening indicator (low-key, `rgba(255,255,255,0.16)`)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56, 72
- Hero: the button is vertically centered (slightly above middle, ~42% from top); prompt 24pt below it
- Sheet rows: 16pt horizontal padding, 8pt vertical internal

### Grid & Container
- The hero has no grid — it is a single centered focal composition
- The bottom sheet uses a single-column list, full width with 16pt margins
- Library grid (optional view): 2-column, 12pt gap

### Whitespace Philosophy
- **Maximal negative space on the hero**: one button, one prompt, vast glowing emptiness — the emptiness *is* the design
- **The sheet is dense by contrast**: history rows pack tightly once revealed, intentionally distinct from the airy hero
- **Nothing competes with the button**: no chrome, no tabs, no banners on the main screen

### Border Radius Scale
- Soft (10-12pt): album art on rows and the result card
- Comfortable (20pt): the result card
- Sheet (24pt): the bottom sheet top corners
- Full Pill (500pt): all buttons, chips, search, the secondary glass actions
- Circle (50%): the Shazam button, the pulse rings, avatars, icon hit targets

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Gradient Base (Level 0) | The radial gradient itself | Hero canvas — no shadow |
| Glass Card (Level 1) | `rgba(255,255,255,0.08)` + 1pt `rgba(255,255,255,0.14)` border | Result card, sheet rows |
| Button (Level 2) | `rgba(0,80,255,0.45) 0 16px 48px` (blue-tinted) | The Shazam button lifts off the gradient |
| Listening Glow | `rgba(0,136,255,0.5)` 60pt blur, animated | Amplified bloom while the mic is open |
| Sheet (Level 3) | `rgba(255,255,255,0.14)` + backdrop-blur 40 + `rgba(0,0,0,0.4) 0 -16px 48px` | Bottom sheet over the gradient |
| Pulse Rings | `rgba(255,255,255,0.22)` strokes fading to 0 | Concentric expansion (animated, not static depth) |

**Shadow Philosophy**: Shazam does not use neutral gray shadows — depth is expressed through *glow* and *glass*. The button casts a blue-tinted shadow that ties it to the gradient rather than separating it from a flat surface; while listening, that shadow becomes an animated glow. Cards are not opaque-with-shadow but translucent glass with a faint white border, so the gradient reads through them. The only conventional dark shadow is under the bottom sheet, lifting it off the void.

### Motion
- **Idle breathing**: button scale 1.0 → 1.04 → 1.0, 2.4s ease-in-out, infinite
- **Concentric ring emission (signature)**: each ring scales 1.0 → 2.6×, opacity 0.22 → 0 over 1.8s, new ring every 0.6s while listening
- **Tap**: button scale 0.94 spring (damping 0.8), `.impactOccurred(.heavy)` haptic, glow blooms
- **Result reveal**: rings collapse, button shrinks up, card springs in (scale + opacity, 0.45s, damping 0.8)
- **Sheet drag**: native detent physics, rubber-band at the top
- **No match**: a single soft horizontal shake of the button + `.notificationOccurred(.error)` haptic

## 7. Do's and Don'ts

### Do
- Use the radial blue gradient (`#0050FF` core → `#0088FF` → `#08090E` edge) as the hero canvas
- Make the Shazam button the single, dominant, centered object — nothing competes with it
- Emit concentric pulse rings outward while listening — this is the signature interaction
- Use translucent glass surfaces (`rgba(255,255,255,0.08)`) so the gradient reads through cards
- Keep secondary text periwinkle (`#B8C4FF`) — it harmonizes with the blue
- Express depth through blue-tinted glow and glass, not gray shadows
- Use Montserrat (SF Pro fallback) at weights 500/700 only
- Trigger a heavy haptic on the Shazam tap, an error haptic on no-match
- Reveal history via an upward-draggable bottom sheet with detents

### Don't
- Don't add a bottom tab bar — the single-purpose hero is the defining choice
- Don't use a flat solid color for the canvas — the radial gradient is the brand
- Don't make surfaces opaque — they must be translucent glass over the gradient
- Don't let any element rival the button for attention on the hero
- Don't use neutral-gray drop shadows — depth is glow and glass
- Don't use white-only secondary text — periwinkle (`#B8C4FF`) is tuned to the gradient
- Don't use thin or black font weights — Montserrat 500/700 only
- Don't animate the rings with bounce — they expand and fade linearly/ease-out
- Don't crowd the hero with banners, counts, or chrome

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Button 116pt; ring max scale 2.4× |
| iPhone 13/14/15 | 390pt | Standard button 132pt; ring max 2.6× |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the transparent top bar |
| iPhone 15/16 Pro Max | 430pt | Button 148pt; ring max 2.8×; result card art 100pt |
| iPad | 768pt+ | Button 180pt centered; result card max 560pt wide, centered |

### Dynamic Type
- Result title, artist, sheet rows, body: full scale
- "Tap to Shazam" prompt + listening label: fixed (centered hero layout is geometry-sensitive)
- The Shazam glyph: fixed proportion of the button (never scales independently)

### Orientation
- Hero (Shazam screen): **portrait-locked** — the centered radial composition is portrait-tuned
- Bottom sheet / library: **portrait-locked**
- Music video result (if shown): **rotates to landscape**

### Touch Targets
- The Shazam button: 132pt — by far the largest tap target in any app; impossible to miss
- "Open in Apple Music" pill: full-width, 50pt+ tall
- Sheet rows: 68pt height, full-row tappable
- Top-bar icons: 26pt glyph, 44pt hit area

### Safe Area Handling
- Top: transparent bar respects safe area; Dynamic Island floats over the gradient (no collision — nothing is there)
- Bottom: the sheet handle respects the home indicator; peek detent sits above it
- The gradient extends fully edge-to-edge under the status bar and home indicator

## 9. Agent Prompt Guide

### Quick Color Reference
- Gradient core: `#0050FF`
- Gradient mid (Shazam Blue): `#0088FF`
- Gradient edge (space tint): `#08090E`
- Blue pressed: `#006FE0`
- Surface glass: `rgba(255,255,255,0.08)`
- Surface glass strong: `rgba(255,255,255,0.14)`
- Divider: `rgba(255,255,255,0.12)`
- Text primary: `#FFFFFF`
- Text secondary (periwinkle): `#B8C4FF`
- Apple Music accent: `#FA243C`
- Error red: `#FF453A`

### Example Component Prompts
- "Create the SwiftUI Shazam hero screen: full-bleed radial gradient background — radial-gradient at 50% 42% from #0088FF (0%) to #0050FF (30%) to #08090E (100%). Centered 132pt circular Shazam button with a white-to-#CFE0FF radial fill and the Shazam glyph in #0088FF. 'Tap to Shazam' in Montserrat 18pt weight 700 #FFFFFF, 24pt below. No tab bar. A blue-tinted shadow rgba(0,80,255,0.45) 0 16px 48px under the button."
- "Build the Shazam listening state: 3 concentric rings emitting from the 132pt button — each ring scales from 1.0 to 2.6× and fades opacity 0.22 → 0 over 1.8s, a new ring launching every 0.6s. A rgba(0,136,255,0.5) 60pt-blur glow blooms around the button. Prompt text reads 'Listening for music…' in Montserrat 16pt weight 700 #FFFFFF."
- "Design the Shazam result card: a rgba(255,255,255,0.08) glass card, 20pt corner radius, 1pt rgba(255,255,255,0.14) border, floating over the blue gradient. 88×88pt album art (12pt radius) leading, title 22pt weight 700 #FFFFFF + artist 14pt weight 500 #B8C4FF. Below: a full-width white 'Open in Apple Music' pill (16pt 700, Apple Music glyph in #FA243C) and a row of glass secondary actions. Card springs up from the button position (0.45s spring, damping 0.8)."
- "Create a Shazam recent-shazam row in the bottom sheet: 68pt height, 52×52pt album art (10pt radius), title 16pt weight 700 #FFFFFF + artist 14pt weight 500 #B8C4FF, trailing time '2h ago' 13pt weight 500 #B8C4FF + 20pt chevron. Pressed background rgba(255,255,255,0.06); 1pt rgba(255,255,255,0.12) divider."
- "Build the Shazam bottom sheet: rgba(255,255,255,0.14) background with a 40-radius backdrop blur over the gradient, 24pt top corner radius, a 36×4pt rgba(255,255,255,0.4) grab handle centered at the top. Detents: peek / half / full. Reveals 'Your Shazams' — the chronological list of matched tracks."

### Iteration Guide
1. The canvas is a radial blue gradient (`#0050FF` core → `#0088FF` → `#08090E`), not a flat color
2. The Shazam button is the single dominant centered object — nothing competes with it; there is NO tab bar
3. Concentric pulse rings emitting outward while listening is the signature interaction — emit, scale 2.6×, fade
4. Surfaces are translucent glass (`rgba(255,255,255,0.08)`) so the gradient reads through
5. Secondary text is periwinkle (`#B8C4FF`), tuned to the blue — never plain white-gray
6. Depth is blue-tinted glow + glass, never neutral-gray shadows
7. Typography: Montserrat (SF Pro fallback), weights 500/700 only, airy spacing on the hero
8. Heavy haptic on tap; error haptic + soft shake on no-match; result springs up from the button
