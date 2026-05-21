# Design System Inspiration of Gas (iOS)

## 1. Visual Theme & Atmosphere

Gas's iOS app is deliberately, unapologetically *joyful*. It is an anonymous compliments app built for high-schoolers, and every design decision pushes toward warmth, play, and a quick dopamine loop — the opposite of a corporate, dark, "serious" product. The screen is a **full-bleed indigo→purple gradient** (`#6C5CE7 → #A06CFF`) from top to bottom, and floating in the center of that purple world is one big, friendly, pure-white **poll card** with very round corners (28pt) and a soft purple-tinted shadow. There is no feed, no infinite scroll, no profiles to stalk. The entire core experience is: *answer a poll about your classmates, one card at a time, then see flames roll in when people pick you.* This is light-UI by design — Gas has no dark mode in spirit; the brand IS the bright gradient.

The signature moment is the **anonymous compliment poll**. A question like "Who has the best smile? 😄" sits in heavy rounded type above **four bold gradient name buttons** in a 2×2 grid, each a different cheerful color from a fixed rotating palette (indigo, pink, yellow, green). You tap a name (positive-only — every question is a compliment), the card springs away, the next one slides in, and a counter advances. The names are pulled from the user's school and reshuffled with a "shuffle" affordance. This poll card — emoji + bold question + four colorful name choices — is the most recognizable artifact in the app and the thing every clone must nail.

The second signature is the **flame 🔥 currency**. Voting earns flames; flames are spent to unlock hints about *who* picked you ("a girl from your grade", "someone whose name starts with J"). The flame count lives in a pill in the top-right, glowing flame-orange (`#FF7A45`) against the purple. It's the game layer — the reason to come back, the reason to invite friends (more friends = more polls = more flames). Color is loud and happy: the indigo/purple gradient is the world, the four choice colors are a fixed festive set, and flame-orange is the only "currency" accent. Type is **Nunito** — a rounded, soft, friendly sans set at heavy weights (800–900) — chosen because it reads as approachable and middle-school-warm, never austere or techy. Everything about Gas is positive, anonymous, and bright.

**Key Characteristics:**
- Full-bleed indigo→purple gradient world (`#6C5CE7 → #A06CFF`) — light-UI by design, no dark mode in spirit
- Big friendly white poll card (`#FFFFFF`, 28pt radius, soft purple shadow) floating centered — one card at a time
- **Anonymous compliment poll** — emoji + bold question + 4 colorful gradient name buttons in a 2×2 grid (the signature)
- Positive-only questions — every poll is a compliment; the tone is "be nice"
- Fixed festive choice palette — indigo / pink / yellow / green gradient buttons, rotating per poll
- **Flame 🔥 currency** (`#FF7A45`) — earned by voting, spent to unlock "who picked you" hints; the dopamine loop
- Shuffle + skip affordances — reshuffle the four names, or skip to the next card
- Heavy rounded Nunito type (800–900) — approachable, warm, middle-school-friendly, anti-corporate
- Springy, game-like motion — cards bounce away, flames count up, confetti on milestones
- No feed, no infinite scroll, no public profiles — a focused, safe, finite loop

## 2. Color Palette & Roles

### Primary (Interactive)
- **Brand Gradient** (`#6C5CE7 → #A06CFF`, 135–180°): The app world background and primary CTA fill.
- **Gas Indigo** (`#6C5CE7`): The brand anchor — gradient top, primary buttons, choice color #1.
- **Gas Purple** (`#A06CFF`): Gradient bottom, lighter brand accent.
- **Gas Violet** (`#7B4DFF`): The on-white brand tint — used for the poll tag, links, accents on the white card.
- **Indigo Pressed** (`#5A4AD1`): Pressed state for indigo buttons/choices.

### Canvas & Surfaces
- **App Background** (gradient `#6C5CE7 → #A06CFF`): The full-bleed world — every screen.
- **Card White** (`#FFFFFF`): The poll card and all primary content cards — bright, high-contrast on purple.
- **Tint Surface** (`#F4F2FF`): Secondary surfaces inside white cards (pressed rows, sub-panels) — a faint lavender.
- **Divider** (`#ECE9FB`): Hairline dividers inside white cards — a soft lilac.
- **Glass Bar** (`rgba(255,255,255,0.16)` + blur): The translucent tab bar over the gradient.
- **Scrim** (`rgba(40,20,90,0.45)`): Dim behind modals/sheets — a purple-tinted scrim, never gray-black.

> Gas is light-UI by design. There is no separate dark theme; the brand identity *is* the bright purple gradient. (If a system-level dark accommodation is ever required, deepen the gradient to `#4A3FB0 → #6C5CE7` and keep cards white — but the product intent is always light.)

### Text (on white cards)
- **Card Text Primary** (`#1A1530`): Poll questions, titles — a near-black plum, warmer than pure black.
- **Card Text Secondary** (`#6B6588`): Body, descriptions — a muted purple-gray.
- **Card Text Tertiary** (`#A29DB8`): Metadata, counts, hints — light lavender-gray.

### Text (on the gradient)
- **On-Purple Primary** (`#FFFFFF`): Top-bar labels, tab labels, skip — pure white.
- **On-Purple Secondary** (`rgba(255,255,255,0.8)`): Sub-labels, de-emphasized.

### Choice Palette (fixed festive set, rotates per poll)
| Slot | Gradient | Text |
|------|----------|------|
| Choice 1 (Indigo) | `#6C5CE7 → #8B6CFF` | `#FFFFFF` |
| Choice 2 (Pink) | `#FF6CC8 → #FF9CDB` | `#FFFFFF` |
| Choice 3 (Yellow) | `#FFC93C → #FFB13C` | `#4A3300` |
| Choice 4 (Green) | `#2BC48A → #4FD9A6` | `#FFFFFF` |

### Accent & Currency
- **Flame** (`#FF7A45`): The flame 🔥 currency color — count pill, "earned", "unlock for N". The single non-purple, non-choice accent. Means exactly "flames".
- **Flame Glow** (`rgba(255,122,69,0.15)`): Lit/active flame chip background.

### Semantic
- **Success** (`#2BC48A`): "Vote counted!", milestone reached (reuses Choice Green for palette discipline).
- **Error** (`#FF5470`): Failed action, "no more polls right now", validation.
- **Celebrate Pink** (`#FF6CC8`): Confetti / celebration accents (reuses Choice Pink).

## 3. Typography Rules

### Font Family
- **Primary**: `Nunito` (by Vernon Adams / Jacques Le Bailly, SIL OFL) — a balanced, rounded, friendly sans-serif. The rounded terminals read as warm and approachable — exactly Gas's middle-school-friendly, anti-corporate voice. Closest free Google Font to Gas's playful rounded type.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Weight philosophy**: Gas runs HEAVY. Most text is 800 (ExtraBold) or 900 (Black). Regular 400 barely appears. The chunky weight is part of the playful identity.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Nunito | 32pt | 900 | 1.15 | -0.4pt | "Polls", big screen titles |
| Screen Title | Nunito | 26pt | 900 | 1.2 | -0.3pt | "Your flames", "Inbox" |
| Poll Question | Nunito | 23pt | 900 | 1.25 | -0.2pt | The poll prompt on the card |
| Card Title | Nunito | 18pt | 800 | 1.3 | -0.1pt | "Someone is thinking about you" |
| Choice Button | Nunito | 16pt | 800 | 1.1 | 0pt | The four name buttons |
| Body | Nunito | 15pt | 700 | 1.45 | 0pt | Card body copy, hints |
| Big Number | Nunito | 28pt | 900 | 1.0 | -0.5pt | Flame counts, big stats |
| Metadata | Nunito | 13pt | 700 | 1.3 | 0pt | "5 people voted · 2m ago" |
| Poll Tag | Nunito | 12pt | 800 | 1.0 | 0.5pt | "ANONYMOUS · BE NICE" uppercase |
| Button | Nunito | 16pt | 900 | 1.0 | 0.2pt | Primary CTA |
| Pill / Flame | Nunito | 15pt | 900 | 1.0 | 0pt | Flame count pill |
| Tab Label | Nunito | 10pt | 800 | 1.0 | 0.1pt | Bottom tab labels |
| Skip / Link | Nunito | 14pt | 800 | 1.0 | 0pt | "Skip ›", text links |

### Principles
- **Heavy and rounded**: 800–900 weight everywhere — the chunkiness IS the brand voice; never set body below 700.
- **Big and centered on cards**: poll questions are large (23pt) and center-aligned — readable at a glance, screenshot-friendly.
- **Emoji as a first-class type element**: a 50pt emoji crowns nearly every poll card; treat it as part of the headline.
- **Numbers shout**: flame counts and stats use 28pt 900 — the game layer should feel celebratory.
- **Dynamic Type**: poll questions, titles, body scale; tab labels, poll tags, choice button labels stay fixed (layout-critical 2×2 grid).

## 4. Component Stylings

### Buttons

**Primary Button (Get my flames / Continue)**
- Shape: Full pill, 999pt corner radius
- Background: brand gradient `#6C5CE7 → #A06CFF`
- Text: `#FFFFFF`, Nunito 16pt weight 900
- Padding: 15pt vertical, 30pt horizontal
- Pressed: `filter: brightness(0.96)` + scale 0.97 (springy)
- Shadow: `0 10px 24px rgba(108,92,231,0.45)` — a colored glow, not gray

**Choice Button (the four poll names)**
- Shape: Large rounded rect, 20pt corner radius (NOT a pill — distinct chunky tile)
- Background: one of the 4 fixed choice gradients (rotates by slot)
- Text: white (or `#4A3300` on the yellow slot), Nunito 16pt weight 800, centered
- Padding: 16pt vertical, 10pt horizontal
- Pressed: scale 0.96 + brightness 0.94; selected: scales 1.04 then springs back as the card exits
- Always rendered in a 2-column grid with 12pt gaps

**Flame Button (Unlock hint)**
- Background: `#FF7A45` (flame)
- Text: `#FFFFFF`, Nunito 15pt weight 900, with a leading 🔥
- Corner radius: 999pt
- Pressed: brightness 0.94 + scale 0.97

**Ghost / Skip Button**
- Background: `rgba(255,255,255,0.18)` (on gradient) OR `#F4F2FF` (on white card)
- Text: `#FFFFFF` (on gradient) / `#6B6588` (on card), Nunito 14–15pt weight 800
- Corner radius: 999pt
- Often just text: "Skip ›" in `#FFFFFF` 80% opacity

**Text Button (Shuffle names)**
- Text: `#7B4DFF` (on card) / `#FFFFFF` (on gradient), Nunito 14pt weight 800
- Often paired with an emoji ("🔄 Shuffle names")
- No background; pressed: 70% opacity

### Core Atoms

**Poll Card (the hero)**
- Background: `#FFFFFF`
- Corner radius: 28pt (very round — friendly)
- Shadow: `0 24px 50px -20px rgba(40,20,90,0.55)` — soft, purple-tinted, generous
- Padding: 26pt top, 20pt sides, 24pt bottom
- Floats centered in the gradient with breathing room above/below
- Content stack: 50pt emoji → 23pt question → 12pt tag → 2×2 choices → footer

**Flame Pill (top-right currency)**
- Background: `rgba(255,255,255,0.22)` (on gradient) or `#FFF` with flame text (on card)
- Content: 🔥 emoji + count, Nunito 15pt weight 900 white
- Corner radius: 999pt, padding 7pt/14pt

**Avatar / Identity**
- Gas is anonymous-first — avatars are rare. Where present: 40pt circle, gradient or emoji fill, never a real photo in poll context

**Card (generic, e.g. inbox item)**
- Background: `#FFFFFF`, 20pt radius
- Soft purple shadow `0 12px 28px -14px rgba(40,20,90,0.4)`
- Internal padding 18–20pt

### Navigation

**Top Bar**
- Height: 44pt + safe area, transparent over the gradient
- Leading: school name (small, 80% white) + screen label (17pt 900 white) stacked
- Trailing: the flame pill
- No border (it floats over the gradient)

**Bottom Tab Bar**
- Height: ~54pt + safe area (70pt visual)
- Background: `rgba(255,255,255,0.16)` + 16px blur (frosted over the gradient)
- 5 slots: Polls, Inbox, **Add/Invite (center, raised white circle)**, School, You
- Icon size: 23pt; center action is a 50pt white circle raised -16pt with an indigo `+` glyph
- Active color: `#FFFFFF` (icons fill solid); inactive: `rgba(255,255,255,0.6)`
- Labels: Nunito 10pt weight 800, always shown (center action has no label)

**Profile / "You"**
- Big flame total (28pt 900), polls answered, school, invite-friends CTA
- Positive-only — no follower counts, no public activity

### Input Fields

**Search (school / friends)**
- Height: 48pt
- Background: `#FFFFFF`, 999pt pill radius
- Border: none (focus: 2pt `#7B4DFF`)
- Leading magnifier `#A29DB8`
- Placeholder: "Find your school" Nunito 15pt weight 700 `#A29DB8`

**Name Entry / Onboarding fields**
- Background: `#FFFFFF`, 18pt radius, generous 16pt padding
- Text Nunito 17pt weight 800 `#1A1530`
- Big, chunky, easy for teens

### Distinctive Components

**Anonymous Compliment Poll (the signature)**
- Emoji headline (50pt) + bold centered question (23pt 900) + uppercase tag ("ANONYMOUS · BE NICE", 12pt 800 `#7B4DFF`)
- Four name buttons in a 2×2 grid, each a fixed-slot gradient (indigo/pink/yellow/green), 20pt radius
- Footer: "🔄 Shuffle names" + a "Skip ›" affordance
- A progress indicator (segmented dots) shows position in the current poll set
- Tapping a name: button springs (scale 1.04), card flies out, next card springs in (~350ms)

**Flame Currency System**
- Flame pill (count) top-right; "+N earned" toasts after voting
- "Unlock for 🔥 N" gates on hint reveals ("who picked you")
- Flame count animates up digit-by-digit with a soft haptic + tiny flame burst

**Choice Result / "Who picked you" Reveal**
- A white card: "Someone picked you for 😄 best smile"
- Blurred/locked hint rows; tapping spends flames to reveal one attribute ("a girl in your grade")
- Reveal animates with a shimmer + confetti pop (`#FF6CC8` / `#FFC93C`)

**Progress Dots**
- Row of segmented bars: 22pt wide, 5pt tall, 3pt radius
- Inactive `rgba(255,255,255,0.35)`; active `#FFFFFF`, widened to 30pt
- Sits below the poll card on the gradient

**Invite / Add Friends (center tab action)**
- Raised 50pt white circle with indigo `+`
- Opens a share sheet — "more friends = more polls = more 🔥" is the core growth loop

**Celebration / Confetti**
- On flame milestones and first poll: a burst of pink/yellow/green confetti over the gradient
- Spring scale-in on the milestone number

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48
- Poll card padding: 26/20/24pt
- Choice grid gap: 12pt
- Card-to-progress-dots gap: 18pt
- Generous vertical centering — the card floats with space above and below

### Grid & Container
- iPhone: single centered poll card, ~18pt side gutters from screen edge to card
- Choices: strict 2×2 grid (never 1×4 or 4×1 — the 2×2 is iconic)
- iPad: card stays centered with a max-width (~440pt) — Gas does NOT spread to a wide grid; it's a phone-shaped, focused experience
- One card on screen at a time — no scroll in the core poll loop

### Whitespace Philosophy
- **The card is the focus**: the purple gradient is generous negative space; the white card is the single object of attention
- **Loud but uncluttered**: bright colors, but only ONE poll card visible — no competing UI
- **Centered and finite**: everything is centered; there is no infinite scroll — the loop is intentionally bounded
- **Playful breathing room**: chunky padding inside the card; nothing cramped

### Border Radius Scale
- Square (0pt): rare — full-bleed gradient only
- Standard (8pt): tiny chips, progress bar segments (3pt)
- Comfortable (14pt): small inner panels
- Choice (20pt): the four name buttons, generic cards
- Card (28pt): the hero poll card
- Sheet (36pt): bottom sheets, modals (very round)
- Pill (500pt): primary CTAs, flame pill, skip, search
- Circle (50%): the center tab action, avatars

## 6. Depth & Elevation

Gas uses depth playfully — the white card *pops* off the purple world with a soft, colored shadow. Shadows are purple-tinted (never neutral gray) so they feel warm and part of the brand.

| Level | Treatment | Use |
|-------|-----------|-----|
| World (Level 0) | The gradient itself, no shadow | App background |
| Floating Card (Level 1) | `rgba(40,20,90,0.55) 0 24px 50px -20px` | The hero poll card |
| Raised Control (Level 1.5) | `rgba(40,20,90,0.5) 0 8px 20px` | The center tab circle, primary CTA glow |
| Generic Card (Level 1) | `rgba(40,20,90,0.4) 0 12px 28px -14px` | Inbox cards, reveal cards |
| Sheet (Level 2) | `rgba(40,20,90,0.5) 0 -8px 30px` + 36pt top radius | Bottom sheets, invite sheet |
| Scrim | `rgba(40,20,90,0.45)` | Dim behind modals — purple-tinted, never gray |

**Shadow Philosophy**: Every shadow is a *purple-plum tint* (`rgba(40,20,90,…)`), not neutral black, so the depth feels cohesive with the gradient world and stays cheerful. The card's big soft shadow makes it feel like a physical card you could pick up — tactile and friendly. Buttons get colored glows matching their fill (indigo CTA → indigo glow), reinforcing the candy-bright feel.

### Motion
- **Card transition (vote)**: tapped choice scales 1.04 (120ms spring), card flies out (translate + fade, 280ms ease-in), next card springs in from below (scale 0.9 → 1.0 + fade, 350ms spring damping 0.7)
- **Choice press**: scale 0.96 + brightness 0.94 (90ms), release springs back
- **Flame count-up**: digits roll/increment over 600ms with a soft haptic per increment + a tiny flame particle burst
- **Skip**: card slides left and fades (220ms ease-in), next springs in
- **Shuffle names**: the four buttons quick-fade-swap labels with a 180ms cross-dissolve + subtle jiggle
- **Confetti / milestone**: pink/yellow/green particles burst from center, milestone number scales 0 → 1.15 → 1.0 (spring)
- **Reveal (who picked you)**: locked row shimmer (800ms), then content fades in + confetti pop
- **Tab switch**: bouncy cross-fade 220ms; active icon pops scale 1.0 → 1.15 → 1.0
- **Haptics**: medium impact on vote, soft tick per flame increment, success haptic on milestone, light tick on skip/shuffle

## 7. Do's and Don'ts

### Do
- Use the full-bleed indigo→purple gradient (`#6C5CE7 → #A06CFF`) as the world — Gas is light-UI by design
- Float ONE big white poll card (28pt radius, soft purple shadow) centered — one card at a time
- Render poll choices as four bold gradient buttons in a strict 2×2 grid
- Keep questions positive-only and centered with a 50pt emoji headline and a "be nice" tag
- Use the fixed festive choice palette (indigo/pink/yellow/green) and rotate by slot
- Make the flame 🔥 (`#FF7A45`) the single currency accent — count pill, earn toasts, unlock gates
- Set type HEAVY in Nunito (800–900) — chunky and rounded is the brand voice
- Use purple-tinted shadows (`rgba(40,20,90,…)`) — never neutral gray
- Make motion springy and celebratory — cards bounce, flames count up, confetti pops
- Keep it finite — one card, no infinite scroll, no public profiles

### Don't
- Don't ship a dark theme — Gas's identity IS the bright purple gradient; light by design
- Don't show a feed or infinite scroll — the loop is one poll card at a time, intentionally bounded
- Don't make questions negative or comparative-in-a-mean-way — every poll is a compliment
- Don't lay choices out 1×4 or in a list — the 2×2 grid of color tiles is iconic
- Don't use neutral gray shadows — all elevation is purple-plum tinted
- Don't go light-weight on type — never below 700; the chunky Nunito is the personality
- Don't add a second accent color beyond the flame — purple gradient + 4 choice colors + flame is the whole system
- Don't reuse flame-orange for anything but the currency — it must mean "flames"
- Don't make it look corporate, minimal, or "sleek" — Gas is candy-bright and teen-warm on purpose
- Don't surface real names/photos in poll context — anonymity and safety are core

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Poll card tighter (22pt padding); question 21pt; choices stay 2×2 |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; flame pill clears it |
| iPhone 15/16 Pro Max | 430pt | Larger card, 26pt question, more breathing room |
| iPad (portrait) | 768pt | Card centered, max-width ~440pt — does NOT widen to a grid; phone-shaped focus |
| iPad (landscape) | 1024pt+ | Same centered card; gradient fills the rest — never a multi-column layout |

### Dynamic Type
- Scales: poll questions, card titles, body
- Fixed: choice button labels (2×2 grid is layout-critical), tab labels, poll tags, progress dots
- Very long names truncate with ellipsis inside the choice tile (never reflow the grid)

### Orientation
- Portrait-only in practice (a one-handed teen app); if rotated, the card stays centered and capped
- No landscape-specific layout — the focused single-card loop is portrait-native

### Touch Targets
- Choice buttons: full tile, ≥ 56pt tall — big, easy, thumb-friendly
- Center tab action: 50pt circle, 56pt hit
- Flame pill: 44pt hit
- Skip / shuffle: ≥ 44pt hit
- Tab items: 23pt glyph, 48pt hit

### Safe Area Handling
- Top: flame pill + school label respect the Dynamic Island
- Bottom: frosted tab bar + home indicator respected; the raised center circle clears the indicator
- Sides: ~18pt gutter from screen edge to the poll card
- The gradient extends fully edge-to-edge under everything (it's the world)

## 9. Agent Prompt Guide

### Quick Color Reference
- App background: gradient `#6C5CE7 → #A06CFF`
- Gas Indigo: `#6C5CE7`
- Gas Purple: `#A06CFF`
- Gas Violet (on-white tint): `#7B4DFF`
- Card white: `#FFFFFF`
- Tint surface: `#F4F2FF`
- Divider (on card): `#ECE9FB`
- Card text primary: `#1A1530`
- Card text secondary: `#6B6588`
- Card text tertiary: `#A29DB8`
- Flame (currency): `#FF7A45`
- Choice 1 indigo: `#6C5CE7 → #8B6CFF` (white text)
- Choice 2 pink: `#FF6CC8 → #FF9CDB` (white text)
- Choice 3 yellow: `#FFC93C → #FFB13C` (text `#4A3300`)
- Choice 4 green: `#2BC48A → #4FD9A6` (white text)
- Success: `#2BC48A` · Error: `#FF5470`

### Example Component Prompts
- "Build the Gas poll card in SwiftUI: a `#FFFFFF` card with 28pt corner radius and a soft shadow `rgba(40,20,90,0.55)` blur 50 y 24, floating centered on a full-bleed gradient `#6C5CE7 → #A06CFF`. Card padding 26pt top / 20pt sides / 24pt bottom. Content stack: a 50pt emoji centered, a poll question in Nunito 23pt weight 900 `#1A1530` centered, an uppercase tag 'ANONYMOUS · BE NICE' in Nunito 12pt weight 800 `#7B4DFF` with 0.5pt tracking, then a 2×2 grid (12pt gaps) of four name buttons. Each button: 20pt corner radius, 16pt vertical padding, Nunito 16pt weight 800 centered, filled with its fixed slot gradient — slot 1 `#6C5CE7→#8B6CFF` white, slot 2 `#FF6CC8→#FF9CDB` white, slot 3 `#FFC93C→#FFB13C` text `#4A3300`, slot 4 `#2BC48A→#4FD9A6` white. Footer: a '🔄 Shuffle names' text button in `#7B4DFF`. On tap, the chosen button scales to 1.04 (spring), the card flies out over 280ms, and the next card springs in from below over 350ms."

- "Create the Gas flame currency pill: a capsule `rgba(255,255,255,0.22)` on the gradient, containing a 🔥 emoji + the count in Nunito 15pt weight 900 white, 7pt vertical / 14pt horizontal padding, 999pt radius. When flames are earned, animate the number incrementing digit-by-digit over 600ms with a soft haptic per tick and a tiny flame-particle burst."

- "Build the Gas bottom tab bar: ~54pt + safe area, background `rgba(255,255,255,0.16)` with a 16px blur over the gradient. Five slots: Polls, Inbox, Add (center — a raised 50pt white circle, offset -16pt up, with an indigo `#6C5CE7` `+` glyph and a `rgba(40,20,90,0.5)` shadow), School, You. Inactive icons `rgba(255,255,255,0.6)` 23pt; active icons solid `#FFFFFF`. Labels Nunito 10pt weight 800 (no label on the center action)."

- "Create the Gas primary button: a full pill (999pt) filled with the gradient `#6C5CE7 → #A06CFF`, white Nunito 16pt weight 900 text, 15pt vertical / 30pt horizontal padding, and a colored glow shadow `rgba(108,92,231,0.45)` blur 24 y 10. Pressed: brightness 0.96 + scale 0.97 (springy)."

- "Build the Gas progress dots: a centered row of segmented bars below the poll card, each 22pt wide × 5pt tall, 3pt radius, color `rgba(255,255,255,0.35)`; the active segment is `#FFFFFF` and widens to 30pt. 6pt gap between segments."

- "Create the Gas 'who picked you' reveal card: a `#FFFFFF` card (20pt radius, purple shadow) titled 'Someone picked you for 😄 best smile' in Nunito 18pt weight 800 `#1A1530`. Below: locked hint rows with a blur + a '🔥 Unlock for 5' flame button (`#FF7A45`, white Nunito 15pt weight 900, 999pt radius). Tapping spends flames; the row shimmers 800ms then reveals the hint text with a pink/yellow confetti pop."

### Iteration Guide
1. The world is a full-bleed indigo→purple gradient `#6C5CE7 → #A06CFF` — Gas is LIGHT-UI by design; never ship a dark theme
2. One big white poll card (28pt radius, soft purple-tinted shadow) floats centered — one card at a time, no feed, no scroll
3. The signature is the anonymous compliment poll: 50pt emoji + bold centered 23pt question + four gradient name buttons in a strict 2×2 grid
4. Choice buttons use a FIXED festive palette by slot — indigo / pink / yellow / green — 20pt radius tiles, never a 1×4 list
5. Every question is positive-only with a "be nice" tag — Gas is compliments, anonymous and safe
6. The flame 🔥 `#FF7A45` is the ONLY currency accent — earned by voting, spent on "who picked you" hints; it animates and celebrates
7. Type is Nunito set HEAVY (800–900) — chunky and rounded is the brand voice; never below 700
8. All shadows are purple-plum tinted (`rgba(40,20,90,…)`) — never neutral gray
9. Motion is springy and celebratory — cards bounce in/out, flames count up, confetti pops on milestones
10. Keep it finite, centered, and phone-shaped even on iPad — no infinite scroll, no public profiles, no corporate sleekness
