# Design System Inspiration of Coffee Meets Bagel (iOS)

## 1. Visual Theme & Atmosphere

Coffee Meets Bagel's iOS app is a *relaxed first date over coffee*, rendered as software. Where swipe-apps are cold and infinite, CMB is warm, curated, and deliberately scarce: you get a small batch of hand-picked "bagels" (matches) once a day. The whole visual language is built from a coffee-house palette — roasted brew-brown (`#A0522D`), a friendly bagel-orange (`#F4623A`), and warm cream (`#F3E4CF`) — over a cozy brown-black canvas (`#14100D` in dark, warm off-white `#FBF6EF` in light). Nothing is stark; even "white" is cream. The atmosphere says *take your time, this is a curated introduction, not a slot machine.*

The signature screen is **Today's Bagels**: a single large profile card at a time, framed in a generous 24pt-radius rounded rectangle with a full-bleed photo, a soft bottom gradient, the person's name + age in Poppins 26pt, a one-line meta row ("Designer · 2 mi away · ☕ Latte person"), and a row of warm interest chips. Below the card sit exactly two primary actions: a roasted-brown circular **Pass** (×) and a noticeably larger bagel-orange circular **Like** (♥). The Like is always the biggest, warmest element on screen — CMB wants you to lean toward connection. A smaller brew-brown **Send a Bagel** (★) action lets you spend beans to stand out. The second core surface is the **curated batch header** ("6 fresh picks · expires in 23h") that frames the day's selection as finite and fresh, and the third is the **profile detail** — a long, scrollable, photo-and-prompt card where the bagel's answers ("My ideal Sunday is…") are rendered as warm cream prompt blocks.

The color system is a tight, food-warm triad. **Bagel-orange** (`#F4623A`, deepening to `#D94E2A`) is the single primary action color: the big Like, the active tab, "fresh" badges, the unread chat dot. **Brew-brown** (`#A0522D`, deep `#7E3F22`) is the secondary brand color: "Send a Bagel", selected interest chips, the beans/currency surfaces. **Cream** (`#F3E4CF` / soft `#E8D4B8`) replaces pure white everywhere — on-photo text, outline buttons, card insets. A muted **like-green** (`#4CC38A`) marks mutual-match confirmations, and **premium gold** (`#E0A82E`) is reserved for the subscription upsell. The discipline keeps the app feeling like one warm, coherent café rather than a loud marketplace.

Typography is rounded and friendly. CMB's product uses a warm geometric sans (Brandon Grotesque / Gotham-family in marketing); **Poppins** is the closest free face and is used here. Hierarchy is people-first: screen title 32pt/800, profile name 26pt/700, section 22pt/700, card title 18pt/600, body 16pt/400, prompt answer 15pt/600, meta 14pt/400, chip 13pt/600, badge 12pt/700 uppercase. Weight concentrates at 400/600/700/800 — regular for prose, semibold for prompts and chips, bold for names and sections, extrabold for screen titles. The rounded letterforms reinforce the cozy, approachable mood.

**Key Characteristics:**
- Coffee-house palette — brew-brown (`#A0522D`) + bagel-orange (`#F4623A`) + cream (`#F3E4CF`) over warm brown-black (`#14100D`)
- Curated, scarce model — a small daily batch of "bagels", framed as fresh and expiring
- Today's Bagels card — one large profile at a time, 24pt-radius, full-bleed photo, bottom gradient
- Two-action commitment — roasted-brown circular Pass + a noticeably larger bagel-orange Like
- Send a Bagel — brew-brown star action, spend beans to stand out
- Warm interest chips — emoji + label, cream on translucent over photo, brown when selected
- Batch header — "6 fresh picks · expires in 23h" — finite, fresh, no infinite feed
- Cream replaces white — on-photo text, outline buttons, prompt blocks; nothing is stark
- Pill buttons & circular actions — fully rounded (999pt) everywhere
- Poppins / Brandon type — rounded, friendly, people-first hierarchy
- Like-green (`#4CC38A`) for mutual match; premium gold (`#E0A82E`) for upsell only
- Mascot warmth — the bagel logomark and gentle copy keep the tone playful, not transactional

## 2. Color Palette & Roles

### Primary (Interactive)
- **Bagel Orange** (`#F4623A`): The single primary action color — the big Like button, active tab, "fresh"/"active" badges, unread chat dot, primary CTAs.
- **Bagel Deep** (`#D94E2A`): Pressed state of the Like / primary buttons, gradient end.
- **Bagel Tint** (`#F4623A1F`): 12% wash behind a just-liked card and the match-confirm flash.

### Secondary (Brand)
- **Brew Brown** (`#A0522D`): Secondary brand color — "Send a Bagel", selected interest chips, beans/currency surfaces, the roasted-brown Pass ring.
- **Brew Deep** (`#7E3F22`): Pressed state of brown buttons, deep accents.
- **Brew Pressed** (`#693318`): Active-press of brown circular actions.

### Cream (replaces white)
- **Cream** (`#F3E4CF`): On-photo text, outline-button text/border, prompt-block background — the app's "white".
- **Cream Soft** (`#E8D4B8`): Secondary on-photo text, dividers over photos.

### Canvas & Surfaces (Light Mode)
- **Canvas Warm** (`#FBF6EF`): Primary light canvas — warm off-white, NOT pure white.
- **Surface 1** (`#FFFFFF`): Cards on light, elevated panels.
- **Surface 2** (`#F3ECE1`): Pressed rows, chip backgrounds, secondary fills.
- **Divider** (`#E8DECF`): Hairline separators.

### Canvas & Surfaces (Dark Mode)
- **Coffee Canvas** (`#14100D`): Primary dark canvas — deep roasted brown-black, NOT neutral grey.
- **Surface 1** (`#1F1813`): Cards, settings rows.
- **Surface 2** (`#2A2017`): Pressed rows, chip backgrounds.
- **Divider** (`#33281D`): Hairline separators (warm brown, not grey).

### Text
- **Text Primary Light** (`#3A2A18`): Body, names — warm dark brown, NOT pure black.
- **Text Secondary Light** (`#8A7458`): Meta, secondary copy.
- **Text Tertiary Light** (`#B0A088`): Placeholders, disabled.
- **Text Primary Dark** (`#EFE6DA`): Body, names on dark — warm cream-white.
- **Text Secondary Dark** (`#B7A48E`): Meta, secondary copy on dark.
- **Text Tertiary Dark** (`#7C6B57`): Placeholders, disabled on dark.

### Action / Status Roles

| Role | Light Mode | Dark Mode | Treatment |
|------|-----------|-----------|-----------|
| Like (primary action) | `#F4623A` | `#F4623A` | Large filled circle, white heart |
| Pass | `#8A7458` on `#F3ECE1` | `#B7A48E` on `#2A2017` | Outline/soft circle, × glyph |
| Send a Bagel | `#A0522D` | `#A0522D` | Brew circle, cream star |
| Mutual match | `#4CC38A` | `#4CC38A` | Match-confirm banner + check |
| Just-liked flash | `#F4623A1F` | `#F4623A24` | 12% orange wash, 500ms fade |

### Semantic
- **Like / Match Green** (`#4CC38A`): "It's a match!" confirmation, mutual-like banner.
- **Error Red** (`#D94E2A`): Destructive confirm, failed-send (shares Bagel Deep family).
- **Warning Gold** (`#E0A82E`): Out-of-beans notice, premium-locked feature.
- **Premium Gold** (`#E0A82E`): CMB Premium upsell, "Boost", subscription surfaces.

## 3. Typography Rules

### Font Family
- **Product**: `Brandon Grotesque` / `Gotham`-family (CMB's warm geometric brand sans) in marketing; rounded geometric sans on app.
- **Closest Free Face**: `Poppins` (by Indian Type Foundry, SIL OFL) — geometric sans with friendly rounded terminals, near-identical warmth.
- **Numeric / Mono (rare)**: `SF Mono` for the beans balance and timers.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Poppins | 32pt | 800 | 1.15 | -0.6pt | "Today's Bagels", "Discover" |
| Profile Name | Poppins | 26pt | 700 | 1.2 | -0.3pt | "Maya, 28" on the card |
| Section | Poppins | 22pt | 700 | 1.25 | -0.2pt | "A little about Maya" |
| Card Title | Poppins | 18pt | 600 | 1.3 | -0.1pt | "Why you matched" |
| Body | Poppins | 16pt | 400 | 1.5 | 0pt | Bio prose, descriptions |
| Prompt Answer | Poppins | 15pt | 600 | 1.4 | 0pt | "My ideal Sunday is…" answer |
| Meta | Poppins | 14pt | 400 | 1.4 | 0pt | "Designer · 2 mi away · ☕ Latte person" |
| Chip | Poppins | 13pt | 600 | 1.0 | 0pt | Interest chips "🥾 Hiking" |
| Badge | Poppins | 12pt | 700 | 1.0 | 0.4pt | "ACTIVE TODAY" uppercase |
| Beans / Number | Poppins | 16pt | 700 | 1.0 | 0pt | Bean balance, batch count |
| Tab Label | Poppins | 11pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Poppins | 16pt | 700 | 1.0 | 0pt | Primary pill label |

### Principles
- **People-first hierarchy**: The profile name is the loudest text on the card; sizes serve the person, not the chrome.
- **Rounded warmth**: The geometric-rounded face (Poppins / Brandon) is intentional — it reinforces the cozy café tone; never substitute a cold grotesque.
- **Weight discipline**: 400 (prose), 600 (chips/prompts/card titles), 700 (names/sections/numbers), 800 (screen titles). No thin, no black, no light.
- **Badges are uppercase 12pt/700**: "ACTIVE TODAY", "NEW" — the only consistently uppercased text, a small confident label.
- **Dynamic Type first-class**: Profile name, section, body, prompt answer scale; chips, badges, bean numbers, tab labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Like / Continue / Match)**
- Shape: Fully rounded pill, 999pt corner radius (or large circle for the card Like action)
- Background: `#F4623A` (Bagel Orange)
- Text/Glyph: `#FFFFFF` — Poppins 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal (circle: 72pt diameter for card Like)
- Pressed: background `#D94E2A` + scale 0.98
- Disabled: background `#F4623A` at 40% opacity

**Secondary Button (Send a Bagel / brand)**
- Background: `#A0522D` (Brew Brown)
- Text: `#F3E4CF` (Cream) Poppins 15pt weight 600
- Corner radius: 999pt
- Padding: 13pt vertical, 24pt horizontal
- Pressed: background `#7E3F22` + scale 0.98

**Outline Button (Pass / Skip)**
- Background: transparent
- Text/Border: `#F3E4CF` (cream) on photos / `#8A7458` on surfaces
- Border: 1.5pt cream / divider
- Corner radius: 999pt
- Padding: 12pt vertical, 22pt horizontal
- Pressed: background `#F3E4CF14` (8% cream)

**Circular Action Buttons (card Pass / Like / Send a Bagel)**
- Pass: 56-60pt circle, `Surface 2` fill, 1.5pt divider border, `×` glyph in Text Secondary
- Like: 68-72pt circle (always the largest), `#F4623A` fill, white `♥` — the visual center of gravity
- Send a Bagel: 56pt circle, `#A0522D` fill, cream `★`
- Shadow: `0 8px 20px -8px rgba(0,0,0,0.4)` light / `rgba(0,0,0,0.6)` dark
- Press: scale 0.92 + soft haptic; Like also triggers a 1.0→1.15→1.0 heart pop

**Text Button (See full profile / Learn more)**
- Font: Poppins 14pt weight 600
- Color: `#F4623A`
- No underline; 12pt vertical hit padding
- Pressed: 60% opacity

### Today's Bagel Card (the core atom)

The defining component — one curated profile at a time.

- Container: 24pt corner radius, full-bleed photo, `overflow: hidden`, shadow `0 16px 40px -16px rgba(0,0,0,0.7)`
- Photo: 3:4 aspect ratio, full-bleed
- Bottom gradient: transparent → `rgba(20,16,13,0.95)` over the lower 55% so text is legible
- Top-left badge: translucent blurred pill (`rgba(20,16,13,0.55)` + blur), cream text 12pt 700, leading icon (e.g., clock for "Active today")
- Info block (bottom-left, 20pt inset): name+age Poppins 26pt 700 (age weight 500) in white, meta row 14pt cream, then interest chips
- Interest chip (on photo): `rgba(243,228,207,0.18)` fill, 1pt `rgba(243,228,207,0.28)` border, cream 13pt 600, 999pt radius, 5×12 padding
- Tap card → expands to full scrollable profile detail (push transition)

### Profile Detail (scrollable)

- Stacked full-width photos (24pt radius, 12pt gap) interleaved with prompt blocks
- Prompt block: cream-tinted card (`#F3E4CF` light / `#2A2017` dark), 16pt radius, prompt label 13pt 600 secondary, answer 15pt 600 primary, 16pt padding
- Sticky bottom action bar with the same Pass / Like / Send-a-Bagel circular trio

### Curated Batch Header

- Title "Today's Bagels" Poppins 19pt 700; subtitle "6 fresh picks · expires in 23h" 12pt secondary
- Trailing 38pt circular icon button (beans / info) `Surface 2` fill
- The "expires in" timer counts down live; at expiry the batch animates out and "Come back tomorrow" empty state appears

### Match Confirmation ("It's a match!")

- Full-screen takeover: warm gradient (`#F4623A` → `#A0522D`), two circular avatars overlapping, "It's a match!" Poppins 32pt 800 white, like-green check accent, "Say hi" primary pill + "Keep browsing" text button
- Confetti of small bagel glyphs, gentle scale-in

### Navigation

**Top Bar**
- Height: 44pt + safe area; leading screen/section title, trailing circular icon (beans balance / filter)
- On profile detail: leading back chevron, trailing report/overflow

**Bottom Tab Bar**
- Height: 56pt + safe area, `#FBF6EF` / `#14100D`, 0.5pt top border
- 4 tabs: Suggested (bagel logomark), Discover (`magnifyingglass`), Chats (`message`, with orange unread dot), Profile (`person`)
- Active color: `#F4623A`; inactive `#B0A088` / `#7C6B57`
- Labels: Poppins 11pt 600, always shown; active icon fills + label goes orange; no tint pill
- Unread badge: 8pt `#F4623A` dot, top-right of the Chats icon, 1.5pt canvas-colored ring

### Input Fields

**Chat Composer**
- Height: 44pt, `Surface 2` fill, no border, 999pt radius
- Placeholder: "Message Maya…" Poppins 15pt secondary
- Trailing send: 36pt `#F4623A` circle with white arrow when text present

**Search (Discover)**
- Height: 40pt, `Surface 2` fill, 999pt radius
- Leading `magnifyingglass` 16pt secondary, placeholder "Search by name or interest" 14pt secondary

### Distinctive Components

**Today's Bagel Card** — see core atom; the defining one-at-a-time curated profile.

**Pass / Like / Send-a-Bagel Trio** — the three circular actions, with Like always the largest, warmest element.

**Curated Batch Header** — finite, fresh, expiring daily selection — the anti-infinite-feed signature.

**Interest Chips** — emoji + label warm pills; cream-on-translucent over photos, brew-brown when selected.

**Beans Currency** — the brew-brown bean economy: a coffee-bean glyph + balance, spent on "Send a Bagel" / Discover likes; surfaces are always brown-toned.

**Match Confirmation Takeover** — warm orange→brown gradient celebration with bagel confetti.

**Couples / Success Stories Strip** — a warm testimonial carousel on the discover/onboarding surface reinforcing the "real relationships" brand.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt
- Card internal inset (over photo): 20pt
- Action-row gap: 28pt between circular actions
- Prompt-block padding: 16pt

### Grid & Container
- iPhone: card at 20pt horizontal insets, 3:4 photo, single column
- iPad: card max-width 480pt centered; profile detail max 600pt; chat list as 360pt left rail + thread
- Chats list: single-column rows, 72pt tall, 20pt insets

### Whitespace Philosophy
- **Scarcity has room**: one bagel card fills the screen with generous margins — the curated model is reflected in the calm, uncrowded layout
- **The person is the hero**: photo is full-bleed; chrome (badges, chips) floats translucently so it never competes with the face
- **Two clear choices**: Pass and Like get their own breathing room and unequal weight — the bigger Like nudges toward connection
- **Warm, never clinical**: generous radii (24pt cards), soft shadows, cream insets — the layout feels like a cozy café table

### Border Radius Scale
- Square (0pt): full-bleed photo edges that meet card edges, dividers
- Subtle (6pt): small inline chips, timers
- Standard (10pt): list rows, small media
- Comfortable (16pt): prompt blocks, settings cards, sheets
- Card (20pt): chat cards, modal sheets
- Hero (24pt): the Today's Bagel card, profile photos
- Pill (999pt): all buttons, interest chips, badges
- Circle (50%): Pass/Like/Send actions, avatars, icon buttons

## 6. Depth & Elevation

CMB uses soft, warm elevation — shadows are brown-tinted and gentle, like lamplight in a café.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, list rows, prompt blocks |
| Card (Level 1) | `rgba(0,0,0,0.7) 0 16px 40px -16px` (dark) / `rgba(58,42,24,0.16) 0 12px 32px -12px` (light) | Today's Bagel card, profile photos |
| Action (Level 2) | `rgba(0,0,0,0.6) 0 8px 20px -8px` | Circular Pass/Like/Send buttons |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -8px 32px` | Bottom sheets (filters, beans store, premium) |
| Modal Scrim | `rgba(20,16,13,0.6)` | Dim behind match takeover & confirm modals |

**Shadow Philosophy**: Elevation makes the bagel card feel like a physical photo resting on a warm table, and the Like button feel like a tactile button you press. Shadows are intentionally brown-warm, never neutral grey. On dark mode shadows deepen to `0.6–0.7` opacity and cards add a subtle 1pt `#33281D` warm border as a secondary lift cue.

### Motion
- **Card advance (Pass/Like)**: liked card scales 1.0→1.04 then flies up-right with a slight rotate over 320ms ease-in; passed card slides down-left over 280ms; next card cross-fades + scales 0.96→1.0 over 260ms ease-out
- **Like heart pop**: the heart glyph scales 1.0→1.15→1.0 over 280ms spring (response 0.3, damping 0.6) + soft success haptic; a 12% orange wash flashes over the card and fades over 500ms
- **Match takeover**: full-screen gradient scales in from 0.9→1.0 + fades over 400ms; bagel confetti falls over 1.2s; avatars settle with a gentle spring
- **Batch expiry**: at 00:00 the timer pulses, the last card fades out, the "Come back tomorrow" empty state cross-fades in over 400ms
- **Chip select**: scale 0.95 tap-down, fill animates `Surface 2` → `#A0522D` over 180ms
- **Tab switch**: cross-dissolve 200ms; active icon does a gentle 1.0→1.1→1.0 pop; unread dot pulses once on new message
- **Profile open**: card expands into the full profile via an iOS push (300ms) with the hero photo shared-element matched
- **Haptic feedback**: soft success on Like, light tick on Pass, medium impact on match takeover, light tap on chip select

## 7. Do's and Don'ts

### Do
- Use a warm coffee-house palette — brew-brown (`#A0522D`) + bagel-orange (`#F4623A`) + cream (`#F3E4CF`) over warm brown-black (`#14100D`)
- Make Bagel Orange the single primary action color — the Like, active tab, fresh badges
- Make the Like button the largest, warmest element on the card — nudge toward connection
- Use Brew Brown as the secondary brand color — Send a Bagel, selected chips, the beans economy
- Replace pure white with cream (`#F3E4CF`) everywhere — on-photo text, outlines, prompt blocks
- Present one curated bagel card at a time with generous margins and a 24pt radius
- Frame the batch as finite and fresh ("6 fresh picks · expires in 23h")
- Use warm interest chips (emoji + label) — translucent cream over photos, brown when selected
- Pop the Like heart (1.0→1.15→1.0) with a soft haptic and a fading orange card wash
- Celebrate a mutual match with a warm orange→brown takeover and bagel confetti
- Keep shadows brown-warm and soft — like café lamplight, never neutral grey

### Don't
- Don't use a cold neutral-grey dark mode — the canvas is roasted brown-black `#14100D`
- Don't make Pass and Like the same size — the Like is intentionally bigger
- Don't build an infinite swipe feed — CMB is curated and scarce by design
- Don't use pure white — cream `#F3E4CF` is the app's "white"
- Don't use pure black text — warm dark brown `#3A2A18` light / cream `#EFE6DA` dark
- Don't use a cold grotesque typeface — the rounded geometric warmth (Poppins/Brandon) is the tone
- Don't use sharp rectangular cards or buttons — generous radii (24pt cards, pill/circle actions)
- Don't let chrome compete with the photo — badges and chips float translucently
- Don't reserve premium gold for anything but the subscription upsell
- Don't animate aggressively — motion is warm and gentle, 180–400ms ease-out with soft haptics
- Don't add a Material-style tint pill to the active tab — icon fills + label goes orange only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card 16pt insets; 2 interest chips max on card; action circles 56/68 |
| iPhone 13/14/15 | 390pt | Standard layout, 20pt insets, 60/72 action circles |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Taller photo; up to 3 chips; slightly larger action circles |
| iPad (portrait) | 768pt | Card max 480pt centered; profile detail max 600pt |
| iPad (landscape) | 1024pt+ | 2-pane: chats list (360pt) + thread; bagel card stays centered max 480pt |

### Dynamic Type
- Profile name, section, body, prompt answer: full scale
- Interest chips, badges, bean numbers, tab labels: FIXED (layout-sensitive)
- Timers / numeric: scale to L but stay tabular

### Orientation
- Portrait-primary (dating context); iPad supports landscape with chats-list + thread split
- The bagel card stays portrait-proportioned (3:4 photo) regardless of orientation; it never stretches wide

### Touch Targets
- Like circle: 68–72pt
- Pass / Send circle: 56–60pt
- Chip: min 32pt tall, full-chip tap
- Tab bar icons: 22pt glyph, 56pt hit
- Card tap (open profile): full card

### Safe Area Handling
- Top: top bar respects safe area and Dynamic Island
- Bottom: action row + tab bar respect home indicator; profile-detail action bar floats above the home indicator
- Keyboard: chat composer lifts above the keyboard; profile editing scrolls above it
- Sides: 20pt screen inset (16pt on SE)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FBF6EF`  ·  Canvas (dark): `#14100D`
- Surface 1: `#FFFFFF` light / `#1F1813` dark
- Surface 2: `#F3ECE1` light / `#2A2017` dark
- Divider: `#E8DECF` light / `#33281D` dark
- Text primary: `#3A2A18` light / `#EFE6DA` dark
- Text secondary: `#8A7458` light / `#B7A48E` dark
- Bagel Orange (primary action): `#F4623A` → deep `#D94E2A`
- Brew Brown (secondary brand): `#A0522D` → deep `#7E3F22`
- Cream (replaces white): `#F3E4CF` / soft `#E8D4B8`
- Match green: `#4CC38A`
- Premium gold: `#E0A82E`
- Just-liked flash: `#F4623A1F`

### Example Component Prompts
- "Build the Coffee Meets Bagel Today's Bagel card in SwiftUI: 24pt corner radius, full-bleed 3:4 photo, `overflow: hidden`, shadow `rgba(0,0,0,0.7) 0 16px 40px -16px`. Top-left translucent badge: `rgba(20,16,13,0.55)` + blur, cream text Poppins 12pt w700, leading clock icon, '·ACTIVE TODAY·'. Bottom gradient transparent → `rgba(20,16,13,0.95)` over lower 55%. Bottom-left 20pt inset: 'Maya' + ' 28' (Poppins 26pt w700, age w500) white; meta 'Designer · 2 mi away · ☕ Latte person' Poppins 14pt cream; then 3 interest chips (`rgba(243,228,207,0.18)` fill, 1pt `rgba(243,228,207,0.28)` border, cream 13pt w600, 999pt radius)."

- "Create the CMB action trio: a centered row, 28pt gap. Pass = 60pt circle, `#2A2017` fill, 1.5pt `#33281D` border, × glyph `#B7A48E`. Like = 72pt circle (the largest), `#F4623A` fill, white ♥ 32pt. Send a Bagel = 56pt circle, `#A0522D` fill, cream ★. On Like tap: heart scales 1.0→1.15→1.0 over 280ms spring, soft success haptic, a 12% `#F4623A` wash flashes over the card fading over 500ms; the liked card scales 1.04 then flies up-right with slight rotate over 320ms, next card cross-fades + scales 0.96→1.0."

- "Render the CMB curated batch header: title 'Today's Bagels' Poppins 19pt w700 `#EFE6DA`, subtitle '6 fresh picks · expires in 23h' Poppins 12pt `#B7A48E` with a live countdown; trailing 38pt circular beans button `#2A2017` fill. At expiry, pulse the timer, fade the last card out, cross-fade a 'Come back tomorrow' empty state in over 400ms."

- "Build the CMB match takeover: full-screen warm gradient `#F4623A` → `#A0522D`, two overlapping 96pt circular avatars, 'It's a match!' Poppins 32pt w800 white with a `#4CC38A` check accent, 'Say hi' primary orange pill + 'Keep browsing' cream text button. Scale in 0.9→1.0 + fade over 400ms; bagel-glyph confetti falls over 1.2s; medium impact haptic."

- "Create CMB interest chips: emoji + label, Poppins 13pt w600, 999pt radius, 8×14 padding. Default `#2A2017` fill, 1pt `#33281D` border, text `#EFE6DA`. Selected `#A0522D` fill + border, text `#F3E4CF`. On tap: scale 0.95 down, fill animates over 180ms, light haptic."

- "Build the CMB profile detail: stacked full-width photos (24pt radius, 12pt gap) interleaved with prompt blocks. Prompt block: `#2A2017` card, 16pt radius, 16pt padding — label Poppins 13pt w600 `#B7A48E`, answer Poppins 15pt w600 `#EFE6DA`. Sticky bottom bar repeats the Pass/Like/Send circular trio. Open via iOS push with the hero photo as a shared element."

### Iteration Guide
1. Canvas is warm brown-black `#14100D` dark / warm off-white `#FBF6EF` light — NOT neutral grey, NOT pure white/black
2. Bagel Orange `#F4623A` is the single primary action color; Brew Brown `#A0522D` is the secondary brand
3. The Like button is always the largest, warmest element — nudge toward connection
4. Cream `#F3E4CF` replaces pure white everywhere (on-photo text, outlines, prompt blocks)
5. Show ONE curated bagel card at a time with generous margins and a 24pt radius — no infinite feed
6. Frame the batch as finite and fresh ("X fresh picks · expires in Yh")
7. Interest chips are warm pills: cream-on-translucent over photos, brew-brown when selected
8. The Like pops the heart (1.0→1.15→1.0) with a soft haptic and a fading orange card wash
9. A mutual match is a warm orange→brown takeover with bagel confetti
10. Motion is gentle: 180–400ms ease-out, soft/medium haptics, brown-warm soft shadows
