# Design System Inspiration of Calm (iOS)

## 1. Visual Theme & Atmosphere

Calm's iOS app is a night sky you can fall asleep inside. The defining surface is a full-screen vertical gradient that runs from a deep twilight blue (`#2A6FD6`) at the top down to a near-black navy (`#0B1E3F`) at the bottom — the color of dusk just after the sun has gone. Over that gradient, almost everything is glass: cards are translucent white (`rgba(255,255,255,0.08)`) so the sky shows through them. The result is an interface that feels less like an app and more like a calm physical space — a planetarium, a lakeside at night — where nothing is sharp, nothing is loud, and the eye is never asked to work.

The accent system is a single, quiet blue. Calm Blue (`#2A6FD6`) is the only saturated color, and it is used sparingly: the primary "Play"/"Start" pill, the active tab, the progress arc on the breathing exercise. Everything else is white at varying opacities — full white for headlines, ~72% white (`#B8C4D8`) for supporting copy. There are no reds, no greens, no semantic traffic lights in the main experience; even errors are stated softly. Photography does the emotional work: full-bleed nature imagery (forests, rain on glass, slow waves) sits behind the content, gently darkened so type stays readable.

Typography is a deliberate pairing. Headlines are set in **Lora**, a contemporary serif with a literary, bedtime-story warmth — "Daily Calm", a sleep story title, the greeting "Good evening". Body and UI text are **Inter**, a clean humanist sans that disappears into the background. The contrast between the two is the brand's voice: the serif speaks (calm, human, narrative), the sans supports (functional, quiet). Sizes are generous and airy — a 30pt serif hero, 17pt body — with weights kept soft (400 / 600 / 700, never heavier). The single most expressive moment is the breathe bubble: a soft white circle that expands and contracts on a 4-7-8 cycle, the type around it fading "Breathe in… Hold… Breathe out…" in time.

**Key Characteristics:**
- Full-screen night-sky gradient (`#2A6FD6` → `#0B1E3F`) as the canvas — dusk, not pure black
- Glass cards — translucent white (`rgba(255,255,255,0.08)`) so the sky shows through
- Calm Blue (`#2A6FD6`) as the single accent — primary actions, active tab, progress arc
- Full-bleed, gently-darkened nature photography behind content
- Serif/sans pairing — **Lora** for headlines (bedtime-story warmth), **Inter** for body
- The breathe bubble — a soft circle that scales on a 4-7-8 breathing loop (signature)
- Daily Calm hero card — the day's featured meditation, large and photographic
- Sleep Story list rows — narrator + duration, designed to be read in the dark
- Bottom tabs (Home / Sleep / Meditate / Music / More) — soft, glass-backed
- Motion is slow and continuous — nothing snaps; everything eases over 600ms+

## 2. Color Palette & Roles

### Primary
- **Calm Blue** (`#2A6FD6`): The single accent — primary "Play"/"Start" pill, active tab icon/label, breathing progress arc, selected state.
- **Calm Blue Pressed** (`#1F57AB`): Pressed/active state for the blue pill and controls.
- **Calm Blue Soft** (`#4A85E0`): Lighter blue used for secondary highlights and the breathe-bubble glow.

### Canvas (Night-Sky Gradient)
- **Gradient Top** (`#2A6FD6`): The twilight-blue top of the full-screen background gradient.
- **Gradient Mid** (`#1A4A8F`): The transitional mid-band of the gradient (~45% down).
- **Gradient Bottom** (`#0B1E3F`): The deep near-black navy at the base — behind the tab bar.
- **Scrim Navy** (`#091830`): Photo-darkening overlay applied beneath text on imagery.

### Surfaces (Glass)
- **Glass Surface** (`rgba(255,255,255,0.08)`): Standard card fill — translucent so the sky shows through.
- **Glass Surface Raised** (`rgba(255,255,255,0.12)`): Higher-elevation cards, the Daily Calm hero, pressed list rows.
- **Glass Divider** (`rgba(255,255,255,0.12)`): Hairline separators between list rows.
- **Glass Hover** (`rgba(255,255,255,0.16)`): Pressed state on glass surfaces.

### Text
- **Text Primary** (`#FFFFFF`): Headlines, card titles, primary copy — pure white for maximum readability on the dark sky.
- **Text Secondary** (`#B8C4D8`): Narrator names, durations, descriptions, supporting copy (~72% white, cool-toned).
- **Text Tertiary** (`#8794A8`): Very low-emphasis metadata, disabled labels, timestamps.

### Semantic (Used Sparingly)
- **Progress Blue** (`#2A6FD6`): Session progress arc / scrubber fill (same as accent).
- **Streak Gold** (`#F2C94C`): Streak flame and "X-day streak" emphasis — the only warm accent, rare.
- **Gentle Error** (`#E08A8A`): Soft desaturated rose for the rare error message — never harsh red.
- **Success Calm** (`#7FB8A0`): Soft sage for "Session complete" confirmation.

### Light Mode
Calm's iOS app is effectively dark-only — the night-sky gradient is intrinsic to the brand and the product is used primarily before sleep. There is no shipped light variant. (A daytime reference would invert to Canvas `#EAF1FB`, Text `#0B1E3F`, but it is not part of the product.)

## 3. Typography Rules

### Font Family
- **Headline / Display**: `Lora` — a contemporary serif with literary, bedtime-story warmth
- **Body / UI**: `Inter` — a clean humanist sans that recedes
- **Fallback (serif)**: `Georgia, 'Times New Roman', serif`
- **Fallback (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Greeting Hero | Lora | 30pt | 600 | 1.25 | -0.2pt | "Good evening, Sam" on Home |
| Screen Title | Lora | 26pt | 600 | 1.3 | -0.2pt | "Sleep", "Meditate" screen heads |
| Daily Calm Title | Lora | 24pt | 600 | 1.3 | -0.1pt | The featured-session title on the hero card |
| Card Title | Lora | 19pt | 600 | 1.35 | 0pt | Sleep-story / meditation card titles |
| Section Header | Inter | 16pt | 700 | 1.3 | 0.2pt | "Sleep Stories", "Featured" labels |
| List Row Title | Inter | 17pt | 600 | 1.35 | 0pt | Sleep-story row name |
| Body | Inter | 17pt | 400 | 1.55 | 0pt | Descriptions, session intros |
| Subtitle / Narrator | Inter | 15pt | 400 | 1.4 | 0pt | "Narrated by Tamara · 32 min" |
| Button (Primary) | Inter | 17pt | 600 | 1.0 | 0.1pt | "Play", "Start session" pill text |
| Meta / Duration | Inter | 13pt | 500 | 1.3 | 0.2pt | "10 min", "Day 214" |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Breathe Cue | Lora | 22pt | 400 | 1.2 | 0.5pt | "Breathe in" / "Hold" / "Breathe out" |
| Timer (large) | Inter | 44pt | 300 | 1.0 | 0pt | The countdown during a session (light weight, airy) |
| Label (UPPER) | Inter | 12pt | 700 | 1.2 | 1.0pt | "DAILY CALM", "NEW" tags |

### Principles
- **Serif speaks, sans supports**: Lora carries every emotional/narrative string (greetings, titles, breathe cues); Inter carries every functional one (buttons, meta, navigation). Never swap them.
- **Generous leading**: Body runs at 1.55 — this is a calm reading surface, not a dense feed.
- **Soft weights only**: Lora at 400/600, Inter at 400/500/600/700. The session timer uses Inter 300 (Light) deliberately — it should feel weightless.
- **Whitespace is the typography**: large type, lots of air, short lines. Nothing is cramped.
- **Color is structural**: text is white or cool-grey; Calm Blue is never used for body text emphasis (use the serif or weight instead).

## 4. Component Stylings

### Buttons

**Primary Play / Start Pill**
- Shape: Full pill, height 52pt
- Background: `#2A6FD6`
- Text: `#FFFFFF`, Inter 17pt weight 600, with a leading `play.fill` SF Symbol 16pt
- Padding: 16pt vertical, 40pt horizontal
- Pressed: background `#1F57AB`, scale 0.97, soft haptic
- Used for: "Play", "Start session", "Begin", "Continue"

**Glass Secondary Button**
- Background: `rgba(255,255,255,0.12)`
- Text: `#FFFFFF`, Inter 16pt weight 600
- Border: 1pt `rgba(255,255,255,0.16)`
- Padding: 14pt vertical, 28pt horizontal, corner radius 26pt (full pill)
- Pressed: background `rgba(255,255,255,0.16)`
- Used for: "Add to favorites", secondary CTAs on cards

**Icon Actions (Favorite, Download, Share, Background-sound)**
- Size: 22pt glyph, 44pt hit area
- Default: `#FFFFFF` at 80% opacity
- Active: `#2A6FD6` (favorite saved, downloaded)
- Spacing: 28pt between icons in the player action row

**Text Button ("See all", "Skip")**
- Font: Inter 15pt weight 600
- Color: `#B8C4D8`
- No background, 44pt hit area

### Cards & Containers

**Daily Calm Hero Card** *(signature)*
- Height: ~220pt, full content width
- Background: full-bleed nature photograph, darkened with a `#091830` → transparent bottom-up gradient (so text is legible)
- Overlay content (bottom-left): "DAILY CALM" label 12pt w700 letter-spaced `#FFFFFF`, then Lora 24pt w600 title, then Inter 15pt `#B8C4D8` subtitle "Today · 10 min"
- Trailing: a 44pt circular glass play button (`rgba(255,255,255,0.16)`, `play.fill` 18pt white)
- Corner radius: 20pt
- Pressed: scale 0.98, subtle brightness lift on the photo

**Sleep Story / Meditation Card (Shelf tile)**
- Width: 160pt, photographic top (1:1 or 4:3), text below
- Background: glass `rgba(255,255,255,0.08)` behind the text zone
- Corner radius: 16pt on the image
- Structure: image → Lora 19pt w600 title (2-line clamp) → Inter 13pt w500 `#B8C4D8` "32 min · Sleep"
- Gap between tiles: 14pt
- Tap: scale 0.97 with soft haptic

**Sleep Story List Row**
- Height: 76pt
- Layout: 56pt rounded thumbnail (12pt radius) → stacked title/narrator → trailing duration + download glyph
- Background: transparent over the gradient; pressed → `rgba(255,255,255,0.12)`
- Title: Inter 17pt w600 white; Narrator: Inter 15pt w400 `#B8C4D8`
- Divider: 1pt `rgba(255,255,255,0.12)` between rows

**Glass Card (generic container)**
- Background: `rgba(255,255,255,0.08)` with a 20pt backdrop blur (the sky blurs through it)
- Border: 1pt `rgba(255,255,255,0.12)`
- Corner radius: 20pt
- Padding: 20pt

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(11,30,63,0.72)` with `.regularMaterial` blur (the deep navy base of the gradient, glassed)
- Tabs: Home, Sleep, Meditate, Music, More
- Icon size: 24pt
- Active color: `#2A6FD6` (icon + label both pick up Calm Blue)
- Inactive: `#FFFFFF` at 60% opacity
- Labels: Inter 11pt w600, always shown
- Active icon: filled SF Symbol; inactive: outlined
- A hairline `rgba(255,255,255,0.12)` top border

**Top Bar**
- Height: 44pt + safe area, transparent (the gradient runs behind it)
- Left: profile avatar (30pt circular) or greeting
- Right: streak flame (`#F2C94C`) with day count, then a settings glyph 22pt white
- No solid background — it floats on the sky; content scrolls beneath

**Player Screen (Full)**
- Full-bleed scene photo or animated gradient, scrim-darkened
- Centered: Lora 24pt w600 title, Inter 15pt narrator
- Large circular scrubber arc (Calm Blue progress) around a central 64pt play/pause
- Below: background-sound selector, timer, favorite — glass icon row
- A soft "x min remaining" caption in Inter 13pt `#B8C4D8`

### Input Fields

**Search Field**
- Background: `rgba(255,255,255,0.10)`
- Height: 46pt, corner radius 23pt (full pill)
- Leading `magnifyingglass` SF Symbol 16pt `#B8C4D8`
- Placeholder: "Search meditations, sleep stories", 16pt `#8794A8`
- Text: `#FFFFFF`
- Focus: border `rgba(255,255,255,0.20)`, no color shift

**Mood Check-in Chip**
- Background: `rgba(255,255,255,0.10)` / `#2A6FD6` selected
- Text: `#FFFFFF`
- Padding: 12pt vertical, 20pt horizontal, corner radius 24pt
- Examples: "Calm", "Anxious", "Tired", "Grateful"

### Distinctive Components

**Breathe Bubble** *(signature)*
- A soft white circle, base diameter ~120pt, on the night-sky gradient with a subtle outer glow (`#4A85E0` blur)
- Animation cycle (4-7-8 default): expand to ~220pt over **4s** (inhale), hold at full for **7s**, contract to 120pt over **8s** (exhale)
- The Lora 22pt cue text crossfades in time: "Breathe in" → "Hold" → "Breathe out"
- Concentric faint rings ripple outward on each inhale
- Motion easing is `easeInOut`, continuous, never linear — it must feel like breath

**Daily Calm Streak**
- A `#F2C94C` flame glyph + "214-day streak" in Inter 13pt w500
- On milestone, a gentle particle shimmer (no confetti — Calm stays serene)

**Session Timer Ring**
- Circular progress arc around the player play button
- Track: `rgba(255,255,255,0.16)`, 3pt; Progress: `#2A6FD6`, 3pt, rounded cap
- Sweeps clockwise over the session length; the center shows `pause.fill` 24pt white

**Soundscape Mixer**
- A glass sheet with horizontal sliders (rain, waves, fire, white noise)
- Slider track `rgba(255,255,255,0.16)`, fill `#2A6FD6`, 22pt white thumb
- Each row: icon + label (Inter 15pt) + slider

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard horizontal margin: 20pt (more generous than typical — Calm breathes)
- Section spacing: 32pt between shelves

### Grid & Container
- Single column, full device width with 20pt horizontal margins
- Horizontal shelves: 14pt gaps, peek the next tile at the right edge
- Daily Calm hero: full content width, the visual anchor at the top of Home
- Player: fully centered, vertically balanced

### Whitespace Philosophy
- **Air is the brand**: large margins (20pt+), generous leading (1.55), 32pt section gaps. The interface should never feel busy — busyness is the opposite of calm.
- **Photography breathes too**: hero imagery is given full width and never crowded by chrome.
- **The player is a sanctuary**: it uses the whole screen for one scene and a few large controls.

### Border Radius Scale
- Soft (12pt): List-row thumbnails
- Standard (16pt): Shelf tiles, glass buttons (when not full-pill)
- Comfortable (20pt): Daily Calm hero, generic glass cards, sheets
- Pill (26pt / full): Primary play pill, glass secondary buttons, search field, chips
- Circle (50%): Play buttons, avatars, the breathe bubble, timer ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Sky (Level 0) | The gradient itself; no shadow | The full-screen background canvas |
| Glass (Level 1) | `rgba(255,255,255,0.08)` + 20pt backdrop blur | Standard cards, list-row press states |
| Glass Raised (Level 2) | `rgba(255,255,255,0.12)` + blur + `rgba(0,0,0,0.25) 0 8px 24px` | Daily Calm hero, featured cards |
| Sheet (Level 3) | `rgba(11,30,63,0.94)` + blur + `rgba(0,0,0,0.4) 0 -16px 48px` | Soundscape mixer, settings sheets |
| Tab Bar Material | `rgba(11,30,63,0.72)` + `.regularMaterial` | Floats above scrolling content |
| Scrim | `#091830` → transparent gradient | Darkens photos beneath text |

**Shadow Philosophy**: Calm avoids hard shadows almost entirely — depth comes from translucency and blur, not from cast shadows. The sky shows through every glass surface, which is what makes the app feel layered yet weightless. The only real shadows are soft, very diffuse (24-48pt blur, low opacity) under the raised hero card and sheets, just enough to lift them off the gradient. Photography uses a scrim gradient (not a shadow) to keep type readable.

### Motion
- **Breathe bubble**: scale 120 → 220pt over 4s ease-in (inhale), hold 7s, 220 → 120pt over 8s ease-out (exhale); continuous loop
- **Card tap**: scale 0.97 → 1.0 over 250ms ease-out, soft haptic
- **Play pill tap**: scale 0.97, background → `#1F57AB`, `.impactOccurred(.soft)`
- **Screen transitions**: cross-dissolve 500-600ms (never a hard slide — Calm fades)
- **Timer ring**: continuous clockwise sweep, linear over session length
- **Photo parallax**: hero imagery shifts ~8pt on scroll for gentle depth
- **Session complete**: soft sage check fades in over 600ms with a single gentle haptic
- **Streak shimmer**: slow particle drift, ~2s, no sharp motion

## 7. Do's and Don'ts

### Do
- Use the night-sky gradient (`#2A6FD6` → `#0B1E3F`) as the canvas — dusk, not pure black
- Make cards glass — `rgba(255,255,255,0.08)` with backdrop blur so the sky shows through
- Reserve Calm Blue (`#2A6FD6`) for primary actions, the active tab, and progress
- Set headlines in Lora (serif) and body/UI in Inter (sans) — never swap the roles
- Give the layout generous air — 20pt margins, 1.55 body leading, 32pt sections
- Animate the breathe bubble on a true 4-7-8 ease-in-out cycle — it must feel like breath
- Scrim-darken photography with a navy gradient so white type stays readable
- Keep motion slow and continuous — 500ms+ cross-dissolves, nothing snaps
- Use the soft session timer in Inter Light (300) — it should feel weightless

### Don't
- Don't use pure black `#000000` as the canvas — Calm is a twilight gradient, not a void
- Don't add saturated accent colors beyond Calm Blue (gold streak is the only rare warm)
- Don't set headlines in a sans — the Lora serif is the brand's voice
- Don't use hard drop shadows — depth is translucency and blur, not cast shadow
- Don't crowd the layout — busyness is the opposite of calm
- Don't animate fast or bouncy — no spring overshoot; everything eases gently
- Don't use harsh red for errors — use the soft desaturated rose, stated quietly
- Don't put solid opaque cards on the gradient — the sky must show through the glass
- Don't make the breathe bubble linear or mechanical — it has to breathe

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 140pt shelf tiles; breathe bubble base 104pt |
| iPhone 13/14/15 | 390pt | Standard 160pt tiles; breathe bubble base 120pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the floating top bar |
| iPhone 15/16 Pro Max | 430pt | 180pt tiles; breathe bubble base 136pt; hero 240pt tall |
| iPad | 768pt+ | 3-column shelves; player centers with max 600pt content width |

### Dynamic Type
- Greetings, titles, body, narrator: full scale (Lora and Inter both)
- Tab labels, duration meta: fixed (layout-sensitive at 11-13pt)
- Session timer: scales modestly, capped at 56pt
- Breathe cue: scales but the bubble geometry stays fixed

### Orientation
- Home / Sleep / Meditate / Music: **portrait-locked** (the gradient is vertical by intent)
- Player: **portrait-locked** — the scene composition assumes vertical
- iPad: **rotates** — landscape widens shelves; the player keeps a centered column

### Touch Targets
- Primary play pill: 52pt tall — generous
- Card play buttons: 44pt circular
- List rows: full 76pt height tappable
- Tab icons: 24pt glyph, 44pt effective hit
- Breathe bubble: not a button (it is ambient) — controls sit below it

### Safe Area Handling
- Top: floating bar respects the notch / Dynamic Island; gradient runs to the top edge
- Bottom: tab bar floats above the home indicator; gradient runs to the bottom edge
- Sides: 20pt content insets
- Player: full-bleed scene ignores safe area for the background; controls inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Gradient top: `#2A6FD6`
- Gradient mid: `#1A4A8F`
- Gradient bottom: `#0B1E3F`
- Glass surface: `rgba(255,255,255,0.08)`
- Glass raised: `rgba(255,255,255,0.12)`
- Divider: `rgba(255,255,255,0.12)`
- Text primary: `#FFFFFF`
- Text secondary: `#B8C4D8`
- Calm Blue (accent): `#2A6FD6`
- Calm Blue pressed: `#1F57AB`
- Streak gold: `#F2C94C`

### Example Component Prompts
- "Create a SwiftUI Calm background: a full-screen vertical LinearGradient from #2A6FD6 (top) through #1A4A8F (45%) to #0B1E3F (bottom), ignoring safe area. All content sits on top of this."
- "Build the Daily Calm hero card: ~220pt tall, full width, a full-bleed nature photo darkened by a #091830-to-transparent bottom-up gradient. Bottom-left overlay: 'DAILY CALM' in Inter 12pt weight 700 letter-spacing 1pt #FFFFFF, then a Lora 24pt weight 600 white title, then Inter 15pt #B8C4D8 'Today · 10 min'. Trailing a 44pt circular glass play button (rgba(255,255,255,0.16), play.fill 18pt white). 20pt corner radius; pressed scales 0.98."
- "Design the breathe bubble: a soft white circle base diameter 120pt on the night-sky gradient with a #4A85E0 outer glow. Animate scale to 220pt over 4s ease-in (inhale), hold 7s, contract to 120pt over 8s ease-out (exhale), looping. A Lora 22pt cue ('Breathe in' / 'Hold' / 'Breathe out') crossfades in time. Concentric faint rings ripple outward on each inhale."
- "Create a Sleep Story list row: 76pt tall, a 56pt rounded thumbnail (12pt radius) leading, stacked title in Inter 17pt weight 600 #FFFFFF and 'Narrated by Tamara · 32 min' in Inter 15pt weight 400 #B8C4D8, trailing a download glyph 22pt white at 80% opacity. Transparent over the gradient; pressed background rgba(255,255,255,0.12); 1pt rgba(255,255,255,0.12) divider below."
- "Build the primary play pill: full pill 52pt tall, background #2A6FD6, leading play.fill SF Symbol 16pt, label in Inter 17pt weight 600 #FFFFFF, padding 16pt vertical / 40pt horizontal. Pressed: background #1F57AB, scale 0.97, soft haptic."

### Iteration Guide
1. Canvas is the night-sky gradient `#2A6FD6` → `#0B1E3F` — never pure black
2. Cards are glass — `rgba(255,255,255,0.08)` + backdrop blur; the sky must show through
3. Calm Blue `#2A6FD6` is the only accent — primary actions, active tab, progress
4. Headlines are Lora (serif), body/UI is Inter (sans) — never swap the two roles
5. Give everything air — 20pt margins, 1.55 body leading, 32pt section gaps
6. The breathe bubble must breathe — a true 4-7-8 ease-in-out cycle, never linear
7. Photography is scrim-darkened with navy, not shadowed — keep white type readable
8. Motion is slow and continuous — 500ms+ cross-dissolves, no spring bounce
9. Calm is dark-only — do not surface a daytime light theme
