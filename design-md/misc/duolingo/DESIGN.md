# Design System Inspiration of Duolingo (iOS)

## 1. Visual Theme & Atmosphere

Duolingo's iOS app is a cartoon classroom in your pocket. The canvas is a clean snow white (`#FFFFFF`) interrupted by oversized blocks of saturated, cheerful color — Feather Green (`#58CC02`) on every primary button, Fox Orange (`#FF9600`) on the streak flame, Bee Yellow (`#FFC800`) on the gem counter, Cardinal Red (`#FF4B4B`) on lost hearts. The palette is bright to the point of playful aggression. This is deliberate: Duolingo is trying to convert a chore (language practice) into a game, and the color system borrows more from mobile puzzle games than from education apps. Nothing is muted, nothing is sophisticated, and everything is tuned to reward.

The mascot, Duo the owl, is the brand's load-bearing element. Duo appears on the empty-state lesson screen, pops in to celebrate a streak milestone, crosses his arms on the loss-of-hearts screen, and cries passive-aggressive pleas into push notifications when you skip a day. Illustrations are rounded, flat-shaded, and friendly — no gradients, no rendered shadows, just bold outlines and joyful geometry. Every character in the cast follows the same visual rule: simple shapes, two or three colors, an expressive face.

Typography is Feather Bold for headlines and the Duolingo wordmark only — a custom face by Johnson Banks and Krista Radoeva (2019) inspired by Duo's wings and chunky enough to read like a kids' book. Body type is DIN Next Rounded Pro, a geometric rounded sans that carries the UI. Everything rounds: the letterforms, the button corners (16pt on cards, 12pt on answer tiles), the skill node circles, the progress ring around the XP meter. Dynamic Type is respected but the app's visual rhythm depends on large glyphs sitting on large buttons, so size 13-32pt is the practical working range. The signature typography moment is the big question card — a word or phrase in 22-24pt Feather Bold centered over an illustration, with tappable answer tiles below.

**Key Characteristics:**
- Snow white canvas (`#FFFFFF`) with saturated branded accents — the opposite of muted
- Feather Green (`#58CC02`) as the primary action, Cardinal Red (`#FF4B4B`) as destructive / wrong
- Duo the owl mascot as the brand's emotional anchor — present on empty states, wins, and nudges
- Gamified HUD: streak fire, gem count, heart lives, XP ring — always visible at the top of Home
- Signature learning path: curved vertical trail of circular skill nodes (filled when complete, locked otherwise)
- Chunky 3D-feeling buttons with a lower shadow ledge — press collapses the ledge for tactile feedback
- Celebration moments: confetti, Duo cheering, juicy springs on level-up and streak milestones
- Haptics + sound paired on every correct/wrong answer, streak save, and gem earn
- Rounded-everything geometry: 16pt cards, 12pt tiles, 500pt pill buttons, 50% skill nodes

## 2. Color Palette & Roles

### Primary
- **Feather Green** (`#58CC02`): The verb color — primary CTAs, "Continue" button, correct answer state, completed skill nodes, XP ring fill.
- **Mask Green** (`#89E219`): Lighter accent — progress bar fills, the top highlight on 3D green buttons.
- **Button Green Shadow** (`#58A700`): The darker ledge below primary green buttons — gives the 3D pressed-surface feel.

### Mascot & Gamification
- **Cardinal Red** (`#FF4B4B`): Wrong answer, lost heart, destructive actions, streak-broken state.
- **Fox Orange** (`#FF9600`): Streak flame, daily goal highlight, "on fire" energy state, hearts-refill CTA.
- **Bee Yellow** (`#FFC800`): Gems currency, reward badges, chest icons, yellow stars on skill completion.
- **Macaw Blue** (`#1CB0F6`): Information, links, speaking-exercise state, "Tip" callouts, checkpoint popups.
- **Beetle Purple** (`#CE82FF`): Super Duolingo (premium) accent — used sparingly on the Super badge, promo CTAs.

### Canvas & Surfaces
- **Snow** (`#FFFFFF`): Primary light canvas.
- **Polar** (`#F7F7F7`): Section backgrounds, locked-lesson cards, subtle dividers between path sections.
- **Swan** (`#E5E5E5`): UI surfaces — answer tile default, skill-node locked fill, progress-bar track.
- **Swan Shadow** (`#C4C4C4`): 3D ledge under neutral tile buttons.

### Text
- **Eel Gray** (`#4B4B4B`): Primary text — question prompts, body copy, labels.
- **Hare Gray** (`#AFAFAF`): Secondary text — helper copy, placeholder, streak-day labels, unselected tab.
- **Wolf Gray** (`#777777`): Tertiary — timestamps, disabled labels.

### Dark Mode
Duolingo dark mode shifts to a blue-tinted navy canvas that keeps the cartoon palette legible without the eye-strain of pure light.
- **Dark Canvas** (`#131F24`): Primary dark background — a blue-black, not true black.
- **Dark Surface 1** (`#1F2C34`): Card / lesson container background.
- **Dark Surface 2** (`#37464F`): Answer tile default fill, higher-elevation surfaces.
- **Dark Divider** (`#37464F`): Hairline separators.
- **Dark Text Primary** (`#FFFFFF`): Body and prompts invert to white.
- **Dark Text Secondary** (`#AFAFAF`): Hare gray carries through.
- Accent colors (green, red, orange, yellow, blue, purple) retain their light-mode hex — the brand stays bright against the dark.

### Semantic
- **Correct** (`#58CC02`): Green fill + checkmark + "Great!" toast on correct answer.
- **Wrong** (`#FF4B4B`): Red fill + "Correct solution:" reveal + shake animation.
- **Neutral Disabled** (`#E5E5E5` fill, `#AFAFAF` text): Locked skill node, disabled answer tile.

## 3. Typography Rules

### Font Family
- **Display / Headlines**: `Feather Bold` (proprietary by Johnson Banks / Krista Radoeva, 2019) — chunky, rounded, inspired by Duo's wings. Used for the wordmark, hero numbers ("7" day streak), and question prompts.
- **Body / UI**: `DIN Next Rounded Pro` (Monotype) — geometric sans with rounded terminals. All buttons, labels, body copy, tab bar labels.
- **Fallback Stack**: `SF Pro Rounded` on iOS (closest free substitute), then `-apple-system, BlinkMacSystemFont, 'Nunito', 'Baloo 2', sans-serif`
- **Non-Latin**: DIN Next Rounded Pro has CJK siblings in Monotype's library; Duolingo ships localized fonts per script

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wordmark | Feather Bold | 34pt | 700 | 1.0 | -0.5pt | The "duolingo" logotype |
| Hero Streak Number | Feather Bold | 56pt | 700 | 1.0 | -1.0pt | The big "7" on streak detail |
| Screen Title | Feather Bold | 28pt | 700 | 1.15 | -0.4pt | "Unit 3", "Shop", "Leaderboard" |
| Question Prompt | Feather Bold | 22pt | 700 | 1.25 | -0.2pt | "Translate this sentence" |
| Section Header | DIN Rounded | 20pt | 800 | 1.2 | -0.2pt | "Daily Quests", "Your League" |
| Skill Node Label | DIN Rounded | 16pt | 800 | 1.2 | 0pt | "Basics 1", "Food", "Travel" |
| Primary Button | DIN Rounded | 17pt | 800 | 1.0 | 0.4pt | "CONTINUE", "CHECK" — tracked + uppercase |
| Answer Tile | DIN Rounded | 17pt | 700 | 1.2 | 0pt | Tap-to-answer word choices |
| Body | DIN Rounded | 16pt | 400 | 1.4 | 0pt | Lesson explanations, tip content |
| Helper | DIN Rounded | 14pt | 500 | 1.3 | 0pt | Sub-copy, "3 of 10 lessons" |
| HUD Number | Feather Bold | 18pt | 700 | 1.0 | -0.1pt | Streak count, gem count, heart count |
| Meta / Timestamp | DIN Rounded | 13pt | 500 | 1.2 | 0pt | "5 min ago", "Day 12" |
| Tab Label | DIN Rounded | 11pt | 700 | 1.0 | 0.2pt | Tab bar labels |
| Label (UPPER) | DIN Rounded | 12pt | 800 | 1.2 | 1.0pt | "SECTION 1", "STAGE 2" on path headers |

### Principles
- **Two families, one voice**: Feather for emotion (titles, numbers, Duo-said-so moments), DIN Rounded for everything else — buttons, tiles, labels.
- **Weight concentrated at 400 / 500 / 700 / 800**: No thin, no black. Bold is the default tone; 800 is the button voice.
- **Uppercase only on button labels**: "CONTINUE", "CHECK", "GOT IT" — tracked at 0.4pt for the chunky-button feel. Never uppercase body copy.
- **Numbers are stars**: The streak, gem, and XP counts live in Feather Bold at sizes 2x the surrounding UI — Duolingo treats numbers as celebratory.
- **Dynamic Type supported on body**: Prompts and body copy scale; button labels, tab labels, and HUD numbers stay fixed to preserve layout.

## 4. Component Stylings

### Buttons

**The 3D Primary Button (The Green Slab)**
The single most recognizable Duolingo component. A chunky button with a visible "ledge" below — a 4pt darker-green shadow that makes the button appear to sit on top of itself.
- Shape: Rounded rectangle, 16pt corner radius, full-width inside 16pt insets (or 56pt tall pill for smaller CTAs)
- Height: 56pt for full-width, 48pt for inline
- Top face: `#58CC02` (Feather Green)
- Bottom ledge: `#58A700`, offset `y: +4pt`, visible via a `boxShadow: 0 4px 0 #58A700` or overlay
- Pressed: scale 1.0 but translate `y: +4pt` so the top face meets the ledge (no visible ledge when pressed) + soft haptic
- Text: DIN Rounded 17pt weight 800 uppercase, white, letter-spacing 0.4pt
- Used for: "CONTINUE", "CHECK", "START", "GOT IT"

**The 3D Secondary / Neutral Button (The Swan Slab)**
Same 3D construction, grayscale.
- Top face: `#FFFFFF`
- Bottom ledge: `#E5E5E5` (Swan)
- Border: 2pt solid `#E5E5E5`
- Text: Eel Gray `#4B4B4B`, same typography as primary
- Used for: "SKIP", "NO THANKS", "MAYBE LATER"

**The 3D Destructive Button (The Red Slab)**
- Top face: `#FF4B4B`
- Bottom ledge: `#E53030`
- Text: white
- Used for: "END SESSION", "CLEAR PROGRESS", rarely used — destructive actions usually live behind confirm sheets

**Icon Actions (Heart, Close, Settings)**
- Size: 24pt glyph, 44pt hit area
- Default: Hare `#AFAFAF` or Eel `#4B4B4B` depending on context
- Active: role color (red heart, orange flame, yellow gem)

**Answer Tile (Tap-to-choose word)**
- Shape: Rounded rectangle, 12pt corner radius
- Default: `#FFFFFF` face + `#E5E5E5` 2pt border + 2pt ledge shadow `#E5E5E5`
- Hover/selected: `#DDF4FF` (light blue tint) face + `#84D8FF` border + Macaw Blue ledge
- Correct after check: `#D7FFB8` (green tint) face + `#58CC02` border + text `#58A700`
- Wrong after check: `#FFDFE0` face + `#FF4B4B` border + text `#EA2B2B`
- Font: DIN Rounded 17pt w700 Eel gray
- Padding: 12pt vertical, 20pt horizontal

### Cards & Containers

**Skill Node (Path Circle)**
The signature Duolingo element. A circular node on the curved learning path, containing an icon and (when completed) a star crown.
- Diameter: 80pt (standard), 96pt (current / "you are here" node pulses to 96pt)
- Completed: `#58CC02` fill + white icon + 3D ledge `#58A700` + yellow crown with star count above
- In-progress: `#58CC02` fill + white icon + 3D ledge + animated pulse ring
- Locked: `#E5E5E5` fill + `#AFAFAF` lock icon + no ledge
- Gold-crowned (completed to max level): `#FFC800` fill + white icon + `#E29F03` ledge + crown badge
- Pressed: scale 0.96 + soft haptic
- Legendary (Super Duolingo exclusive): `#CE82FF` fill + white + sparkle overlay

**Lesson Card (Home unit header)**
- Background: Brand color for that unit (often green but varies per unit)
- Height: 140pt, full-width minus 16pt margins
- 16pt corner radius
- White Feather Bold title, 22pt
- Subtitle DIN Rounded 15pt w500 white 85% opacity
- Right side: illustration of Duo or the unit mascot

**Question Card (Lesson screen)**
- Full-width, white canvas
- Top 40%: illustration or speech-bubble with the question subject
- Center: prompt in Feather Bold 22pt Eel, centered
- Below: 2-4 answer tiles laid out in a 2-column grid (tap-to-select) or linear (tap-to-translate)
- Bottom: fixed CHECK button (disabled gray until an answer is selected, primary green when ready)

**XP Ring Card (Home HUD / profile)**
- Circular progress ring, 48pt diameter, 4pt stroke
- Track: `#E5E5E5`
- Fill: `#58CC02` (rotates from top, 0 to 360deg with animation)
- Center: XP number in Feather Bold 14pt

**HUD Chip (Streak / Gems / Hearts)**
- Pill shape, 32pt tall, 500pt corner radius
- Icon left (20pt) + number right in Feather Bold 18pt
- Streak: flame icon `#FF9600` + number in Eel (`#4B4B4B` light / white dark)
- Gems: diamond icon `#1CB0F6` + number
- Hearts: heart icon `#FF4B4B` + number (or infinity symbol for Super users)
- Background: transparent in HUD, `#F7F7F7` in detail cards

### Navigation

**Bottom Tab Bar**
- Height: 60pt + safe area
- Background: `#FFFFFF` with 1pt top border `#E5E5E5`
- Tabs (5): Learn (path), Leaderboard (trophy), Quests (scroll/target), Shop (heart+gem), Profile
- Icon size: 28pt
- Active color: Feather Green `#58CC02` with a soft green tint background pill (36pt height, 500pt radius, `#E5F8D5` fill) behind the active tab
- Inactive: Hare `#AFAFAF`
- Labels: hidden by default on Duolingo's current iOS app (icon-only), optional 11pt DIN Rounded w700 below icon
- Active tab: filled SF Symbol equivalent; inactive: outlined

**Top HUD (Home screen)**
- Height: 56pt + safe area
- Flag (28pt circular) left-aligned — language selector
- Streak chip, gems chip, hearts chip — right-aligned, 12pt gap between
- Background: transparent over scroll with blur on scroll-up
- The flag tap opens a bottom sheet language switcher

**Lesson Top Bar (during active lesson)**
- Height: 56pt + safe area
- Close (X) icon left — 24pt Eel gray, 44pt hit area
- Progress bar center — 12pt tall, 500pt radius, `#E5E5E5` track, `#58CC02` fill with `#89E219` top highlight
- Hearts counter right — heart icon + number

### Input Fields

**Text Input (Free-type answer)**
- Height: 56pt minimum (multi-line grows)
- Background: `#FFFFFF`
- Border: 2pt solid `#E5E5E5` default, `#84D8FF` focused, `#58CC02` correct, `#FF4B4B` wrong
- Corner radius: 12pt
- Font: DIN Rounded 17pt w700 Eel gray
- Placeholder: `#AFAFAF`

**Word Bank (Tap-to-build sentence)**
- Tiles arranged in two rows — available words on bottom, selected words on top
- Each tile is an Answer Tile (see above)
- Tapped tile flies to the top row; tap again to return

### Distinctive Components

**The Learning Path**
Duolingo's signature navigation. A vertically-scrolling curved trail of skill nodes — the path snakes left-right as you scroll down. Each "Section" has a colored hero card above it. Locked nodes below the current progress point show faded with a lock icon.
- Path stroke: 8pt wide dashed line or dotted line `#E5E5E5` connecting nodes
- Nodes: 80pt circles spaced ~96pt apart vertically, offset left-right by ~60pt in an alternating wave
- Current node ("You are here"): pulsing ring animation, 96pt size
- "JUMP HERE" arrow appears beside the current node to pull the eye

**Streak Flame Counter**
The heart of Duolingo's habit loop.
- Fox Orange flame icon (20pt in HUD, 120pt on streak detail)
- Number in Feather Bold, Fox Orange, sized to flame
- On streak-save moment: flame scales 1.0 → 1.2 → 1.0 spring with confetti particles + success haptic + cheer sound
- On streak-broken: flame goes gray `#AFAFAF` with a crack overlay; "Duo is sad" illustration

**Duo The Owl (Mascot Placement)**
- Empty-state illustration: Duo waving, ~200pt tall, centered above CTA
- Celebration overlay: Duo dancing, confetti particles, 280pt tall, paired with haptic success and cheer sound
- Lesson intro: Duo in corner peeking in, ~80pt, with speech bubble containing a tip
- Streak-break push: Duo crying illustration in notification artwork

**Celebration Moment (Level Complete)**
- Full-screen takeover: `#58CC02` background
- Duo illustration center (cheering pose), 280pt
- "LESSON COMPLETE!" in Feather Bold 28pt white
- XP earned: "+10 XP" in Feather Bold 22pt white with Bee Yellow glow
- Confetti particles (8-12 pieces) fall from top for 1.2s
- Haptic: notification success + light impact on each confetti piece
- Sound: brass-band cheer stinger
- Big CHECK button ("CONTINUE") at bottom

**Leaderboard Row**
- Height: 72pt
- League icon (Bronze/Silver/Gold/Sapphire/Ruby/Emerald/Amethyst/Pearl/Obsidian/Diamond) 28pt leading
- Position number in Feather Bold 20pt
- Avatar (40pt circle)
- Username + XP
- Trailing XP number
- Own row: highlighted with `#DDF4FF` background and blue border

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 72, 96
- Standard screen margin: 16pt horizontal
- Path node vertical gap: ~96pt center-to-center
- Card stack gap: 16pt between cards

### Grid & Container
- Content width: device width minus 16pt horizontal insets
- Path: centered with ~120pt horizontal range for left-right wave offset
- Answer tiles in 2-column grid: 12pt gap, equal width
- Tile row (single line): horizontal scroll with 8pt gap, peek last tile

### Whitespace Philosophy
- **Breathable lesson UI**: Question cards use generous vertical whitespace — illustration takes 40%, prompt 20%, tiles 25%, CTA 15%. Never crowded.
- **Dense path view**: The path packs 8-12 nodes in a viewport to signal progression momentum
- **HUD is tight**: Streak/gems/hearts chips sit close together in the top-right to feel like a status-bar dashboard

### Border Radius Scale
- Soft (4pt): Small badges, inline labels
- Tile (12pt): Answer tiles, input fields
- Card (16pt): Lesson cards, primary buttons, sheets
- Unit Hero (20pt): Top-of-section hero cards
- Full Pill (500pt): HUD chips, progress bars, legend ranks
- Circle (50%): Skill nodes, avatars, tab-bar active pill

## 6. Depth & Elevation

Duolingo's depth model is not shadows — it's **ledges**. Every interactive element sits on a darker-colored lower-layer ledge, 4pt tall, creating a 3D "chunky button" feeling that collapses on press.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No ledge, no shadow | Body text, labels, icons |
| Tile Ledge (Level 1) | `0 2px 0` darker-tone | Answer tiles, HUD chips |
| Button Ledge (Level 2) | `0 4px 0` darker-tone | Primary buttons, skill nodes |
| Card Drop (Level 3) | `0 2px 6px rgba(0,0,0,0.06)` | Lesson unit cards on Home |
| Sheet (Level 4) | `0 -8px 24px rgba(0,0,0,0.12)` | Bottom sheets, modals |
| Modal Overlay | `rgba(0,0,0,0.5)` | Behind celebration takeover, dialogs |

**Ledge Philosophy**: The ledge is not a traditional Material shadow — it's a solid-color slab offset straight down, with no blur. When pressed, the top face translates down 4pt to meet the ledge, and the ledge "disappears" behind the face. This mimics the physical sensation of pressing a stiff arcade-game button and is the single most satisfying micro-interaction in the app.

### Motion
- **Button press**: top face translates y+4pt in 80ms linear; ledge hidden. On release: spring back over 250ms damping 0.7, paired with `.impactOccurred(.soft)` haptic
- **Correct answer**: tile turns green + checkmark pops in with scale 0 → 1.2 → 1.0 spring (300ms); success haptic + chime sound
- **Wrong answer**: tile turns red + shakes horizontally ±6pt for 400ms; warning haptic + buzz sound
- **Skill node tap**: scale 0.96 → 1.0 over 180ms spring + light haptic; if unlocking, celebration sequence plays
- **Level complete**: crossfade to green canvas 300ms, Duo slides up from bottom spring 500ms, confetti particles emit for 1.2s, cheer sound, success haptic
- **Streak flame**: idle gentle pulse (scale 1.0 ↔ 1.05 over 2s ease-in-out); on save-moment, confetti burst + scale 1.3 → 1.0 spring
- **Path scroll**: 60fps with occasional paper-texture parallax; current node's pulse ring continues regardless of scroll
- **XP ring fill**: stroke dash-offset animates from 0 → target on lesson complete, 800ms ease-out + haptic on each quarter

## 7. Do's and Don'ts

### Do
- Use Snow white (`#FFFFFF`) as the primary canvas — not off-white, not beige
- Reserve Feather Green (`#58CC02`) for the primary action and correct state — it's the verb
- Build buttons with the 3D ledge (4pt darker-tone offset below) — the chunky feel is the brand
- Pair every correct/wrong answer with both a haptic and a sound — the game feel needs both channels
- Let Duo the owl carry emotional moments — empty states, celebrations, streak saves, loss screens
- Use Cardinal Red (`#FF4B4B`) for wrong answers, lost hearts, and destructive only — nothing else
- Keep the HUD (streak, gems, hearts) visible on Home — it's the habit-loop dashboard
- Round everything: 16pt cards, 12pt tiles, circular nodes, pill chips — no sharp corners on interactive surfaces
- Use Feather Bold for numbers and headlines; DIN Rounded for everything else
- Celebrate wins loudly — confetti + haptic + sound + Duo animation on milestones

### Don't
- Don't use sophisticated or muted palettes — Duolingo is deliberately bright and playful
- Don't use shadows as your depth model — use solid-color 4pt ledges instead
- Don't use pure black text (`#000000`) for body — use Eel (`#4B4B4B`) for softer warmth
- Don't add more accent colors — the 6-role palette (green/red/orange/yellow/blue/purple) is complete
- Don't use thin or light font weights — Duolingo type starts at weight 400 and lives at 700-800
- Don't hide the streak — the streak is the app's emotional core, always surface it
- Don't use long sentences on buttons — "CONTINUE", "CHECK", "GOT IT" — single-verb caps only
- Don't animate Duo subtly — when Duo appears, Duo is a personality, not a decoration
- Don't use system-default iOS alerts for celebrations — build the full-screen takeover with confetti
- Don't make the path straight — the curved wave is recognized, a straight list feels like email

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Path nodes at 72pt; horizontal offset reduced to ±40pt |
| iPhone 13/14/15 | 390pt | Standard 80pt nodes; path offset ±60pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top HUD |
| iPhone 15/16 Pro Max | 430pt | 88pt nodes; question illustration scales to 280pt |
| iPad | 768pt+ | 2-column path (dual-track layout); answer tiles in 4-column grid |

### Dynamic Type
- Question prompts, body, lesson explanations: full scale
- Button labels: fixed (uppercase layout is tight)
- HUD numbers: fixed (dashboard alignment)
- Tab labels: fixed
- Skill node labels: scale up to XL only (layout-sensitive)

### Orientation
- All screens: **portrait-locked** (Duolingo is one-hand usage)
- Match / word-bank exercises do allow landscape on iPad for dual-column layout

### Touch Targets
- Primary button: 56pt tall — impossible to miss
- Answer tiles: 48pt minimum tall
- Skill nodes: 80pt (well above the 44pt HIG minimum)
- Icon buttons: 44pt hit area with 24pt glyph
- HUD chips: 32pt tall but 44pt effective tap

### Safe Area Handling
- Top: HUD respects the safe area and Dynamic Island; chips align to the trailing edge
- Bottom: tab bar respects the home indicator; during active lesson the CHECK button sits just above the safe area
- Sides: 16pt content insets on all scrollable screens

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#131F24`
- Polar (section bg): `#F7F7F7`
- Swan (tile fill): `#E5E5E5`
- Eel (primary text): `#4B4B4B`
- Hare (secondary text): `#AFAFAF`
- Feather Green (primary): `#58CC02`
- Button Green Shadow: `#58A700`
- Mask Green (highlight): `#89E219`
- Cardinal Red (wrong): `#FF4B4B`
- Fox Orange (streak): `#FF9600`
- Bee Yellow (gems): `#FFC800`
- Macaw Blue (info): `#1CB0F6`
- Beetle Purple (Super): `#CE82FF`

### Example Component Prompts
- "Build a Duolingo 3D Primary Button: 56pt tall, full-width inside 16pt margins, 16pt corner radius. Top face `#58CC02`, bottom ledge `#58A700` offset `0 4px 0`. Label 'CONTINUE' in DIN Rounded 17pt weight 800 uppercase white, letter-spacing 0.4pt. On press: translate y +4pt so ledge disappears behind face, soft haptic, 80ms duration. On release: spring back 250ms damping 0.7."
- "Create a Duolingo skill node: 80pt circle, `#58CC02` fill with `#58A700` 4pt ledge shadow, white SF Symbol 'star.fill' 32pt centered. Above node: yellow crown badge at 16pt with star count '3'. Tap: scale 0.96 + light haptic."
- "Render the learning path: vertically scrolling centered trail of 80pt circular skill nodes, offset alternately left ±60pt, right ∓60pt with 96pt vertical spacing. Dashed 8pt `#E5E5E5` connector line between nodes. Current node pulses 1.0 ↔ 1.05 with a `#89E219` ring."
- "Build the Duolingo HUD: horizontal row of three pill chips at 32pt tall, 12pt gap. Streak chip: flame icon `#FF9600` 20pt + number '12' in Feather Bold 18pt Eel gray. Gems chip: diamond icon `#1CB0F6` + '245'. Hearts chip: heart icon `#FF4B4B` + '5'. All on transparent background with leading flag (28pt circular) for language."
- "Create an answer tile: rounded rectangle 12pt radius, 2pt border `#E5E5E5`, 2pt ledge `#E5E5E5`, white fill. Label DIN Rounded 17pt w700 Eel gray, 20pt horizontal 12pt vertical padding. Selected state: `#DDF4FF` fill, `#84D8FF` border. Correct: `#D7FFB8` fill + `#58CC02` border. Wrong: `#FFDFE0` fill + `#FF4B4B` border + 400ms horizontal shake ±6pt."
- "Build the celebration screen: full-bleed `#58CC02` background, Duo cheering illustration 280pt centered, 'LESSON COMPLETE!' in Feather Bold 28pt white centered below, '+10 XP' in Feather Bold 22pt white with Bee Yellow glow, confetti particles (8-12) falling from top over 1.2s, success haptic + cheer sound, CONTINUE button fixed at bottom."

### Iteration Guide
1. Canvas is Snow white (`#FFFFFF`) in light and blue-tinted navy (`#131F24`) in dark — never true black
2. Feather Green (`#58CC02`) is the verb: primary buttons, correct answers, completed nodes — nothing else
3. The 3D ledge button is the signature: every primary interactive uses a 4pt darker-tone slab underneath
4. Duo the owl is not a decoration — Duo is the brand's emotion carrier (empty states, wins, nudges)
5. Typography: Feather Bold for headlines + numbers, DIN Rounded for UI, weights 400/500/700/800 only
6. Button labels are ALWAYS uppercase, always tracked 0.4pt, always DIN Rounded 800
7. Every correct/wrong answer must pair a haptic + sound + color change + animation — game feel lives in all four channels
8. The streak is sacred: surface it on Home, celebrate saves with confetti, mourn breaks with sad-Duo
9. The path is curved, not straight — left-right wave offset of ±60pt is the recognizable signature
10. Round everything: 16pt cards, 12pt tiles, circular nodes, 500pt pills — no sharp corners on interactives
