# Design System Inspiration of Bumble (iOS)

## 1. Visual Theme & Atmosphere

Bumble's iOS app is unapologetically loud. Where Hinge whispers on cream paper and Tinder neon-flames at you, Bumble paints the screen Bumble Yellow (`#FFC629`) — a saturated, sun-bright honey-yellow — and lets it bake. The yellow is everywhere: the splash, the loading dots, the active tab, the primary CTA, the match badge, the hexagon honeycomb that frames every photo and notification. The brand is a single color used with stadium-rock confidence, paired with a near-black warm canvas to keep contrast brutal. If Hinge is a journal, Bumble is a billboard.

The hexagon is the second piece of the identity — Bumble's spatial signature. Photos are hex-cropped on match notifications and the "people near you" grid. The app icon itself is a yellow hexagon with a bee. Cards in the swipe stack are rectangular for usability, but the chrome around them — the badges, the heart, the match indicator, the message preview avatars — all live inside hexagons or hex-tagged frames. The hexagon means "Bumble" the way the heart means "love"; it's the iconography of the brand.

Typography is dominated by Brando — the proprietary slab-influenced sans Bumble commissioned from Mike Abbink — a face with a soft humanist warmth and big personality at display sizes. Headlines like "It's a Match!" and "Make the first move" are set in Brando Black (900) at 32-44pt, sized to hit. Below display, the app uses a heavier weight ladder than most dating apps — Bold (700) for card names, Medium (500) for body, Regular (400) only for very secondary metadata. The voice the type carries is feminist-direct: Bumble's premise (women message first) shapes the typography too — confident, large, no apologies.

**Key Characteristics:**
- Bumble Yellow (`#FFC629`) as the dominant brand color — splash, primary CTAs, tab indicator, match notification, loading hexagons
- Honey Deep (`#F5B616`) for pressed states and the "send a Compliment" premium moments
- Warm Bumble Black (`#1F1F1F`) — slightly warm near-black for type and dark surfaces, never pure
- Canvas: `#FFFFFF` on Bumble Date mode; subtle Cream Tint (`#FFFCF2`) on BFF mode; `#FFFFFF` with yellow accents on Bizz mode
- Hexagon iconography everywhere — photos hex-cropped on notifications, app icon is a hex with a bee, the loading state is rotating hexes
- Swipe stack with big confident photos, name+age at the bottom, big circular buttons (X / heart / yellow rocket / yellow star)
- The yellow "Make the first move" CTA on the empty-message screen — possibly the most iconic Bumble screen
- 5 bottom tabs: People / Hives / Matches / Chats / Profile — with the Hives icon a literal honeycomb
- Brando at weights 400/500/700/900 — heavy ladder for confidence
- Hard edges and big shadows — Bumble is unafraid of presence

## 2. Color Palette & Roles

### Primary
- **Bumble Yellow** (`#FFC629`): The brand color — splash, primary CTAs ("Send", "Continue", "Make the first move"), active tab indicator, match badge, hexagon loading state, "1" badge counter on chats.
- **Honey Deep** (`#F5B616`): Pressed state on yellow CTAs; the "Send a Compliment" premium accent.
- **Yellow Light** (`#FFE9A1`): Yellow chip backgrounds, "Today's Bee" callout fill, soft yellow gradients on premium sheets.

### Brand Modes
- **Date Mode** (`#FFC629` Yellow): The flagship Bumble dating product.
- **BFF Mode** (`#11AAA8` Teal): The friend-finding mode — teal hexagon icon, teal CTA on BFF screens.
- **Bizz Mode** (`#FF8000` Orange): The professional networking mode — orange hexagon icon, orange CTA on Bizz screens.
- The active mode's color replaces all yellow chrome — but the hexagon shape is constant.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas on Date.
- **BFF Cream** (`#FFFCF2`): Subtle warm-tinted canvas on BFF mode.
- **Surface 1** (`#F5F5F5`): Section backgrounds, input fills, settings rows.
- **Surface 2** (`#EDEDED`): Pressed states, chip fills.
- **Divider** (`#E5E5E5`): 0.5pt hairlines, card outlines.
- **Shadow Black** (`rgba(0, 0, 0, 0.12)`): Big confident shadows — Bumble does not whisper.

### Text
- **Bumble Black** (`#1F1F1F`): Primary text — name+age, card titles, body, large display.
- **Bumble Slate** (`#5A5A5A`): Secondary text — bios, "x miles away", chat preview.
- **Bumble Mist** (`#9C9C9C`): Tertiary text — placeholders, disabled labels, very-secondary meta.
- **Pure Yellow Black** (`#000000`): Text on Bumble Yellow surfaces — yellow needs full-black contrast.

### Semantic
- **Match Pink** (`#E94B7B`): Reserved for the "It's a Match!" celebration heart; not used in chrome — the match heart is the only place this pink appears.
- **Verified Blue** (`#0066FF`): Verified profile checkmark.
- **Warning Amber** (`#FF9500`): Profile incomplete warnings.
- **Error Red** (`#D72638`): Form validation errors, unmatched-from-chat confirmations.
- **Success Green** (`#00A86B`): Confirmation toasts ("Message sent", "Profile updated").

### Dark Mode
Bumble added dark mode in 2021. The yellow stays yellow — but everything else inverts to a warm OLED dark.
- **Dark Canvas** (`#0F0F0F`): Primary dark background.
- **Dark Surface 1** (`#1A1A1A`): Cards, sheets.
- **Dark Surface 2** (`#2A2A2A`): Pressed states, chip fills.
- **Dark Divider** (`#2F2F2F`): Hairlines.
- **Dark Text Primary** (`#F2F2F2`): Inverted primary text.
- **Dark Text Secondary** (`#9C9C9C`): Inverted secondary.
- **Bumble Yellow (dark)** (`#FFC629`): Unchanged — yellow reads loud on dark canvas, no adjustment needed.
- **Honey Deep (dark)** (`#FFD45C`): Brightened slightly for OLED on dark mode pressed states.

## 3. Typography Rules

### Font Family
- **Primary**: `Brando` — proprietary slab-influenced sans by Mike Abbink, commissioned for Bumble
- **Weights available**: Regular (400), Medium (500), Bold (700), Black (900) — Bumble does not use Light or Thin; the brand is heavy
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', sans-serif`
- **Web/marketing Google Fonts substitute**: `Bricolage Grotesque` or `Manrope` Heavy — both capture Brando's mix of personality and weight
- Use Bumble Bold or Black for almost every headline; reserve Medium and Regular for body and metadata

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Match Hero | Brando | 44pt | 900 (Black) | 1.05 | -0.8pt | "It's a Match!" celebration title |
| Display | Brando | 32pt | 900 | 1.1 | -0.6pt | "Make the first move", "Find your Hive" |
| Screen Title | Brando | 24pt | 900 | 1.15 | -0.4pt | "Matches", "Chats", "Profile" (top header) |
| Card Name+Age | Brando | 28pt | 700 (Bold) | 1.1 | -0.4pt | "Sigrún, 28" on swipe card |
| Section Header | Brando | 18pt | 700 | 1.2 | -0.1pt | "About me", "Looking for" |
| Body | Brando | 16pt | 500 (Medium) | 1.4 | 0pt | Bio text, profile descriptions |
| Body Bold | Brando | 16pt | 700 | 1.4 | 0pt | Emphasis within body |
| Body Small | Brando | 14pt | 500 | 1.4 | 0pt | "x miles away", chat list preview text |
| Button (Primary) | Brando | 16pt | 700 | 1.0 | 0pt | "Send", "Continue", "Make the first move" |
| Button (Large CTA) | Brando | 18pt | 700 | 1.0 | 0pt | "It's a Match — Send a Message" |
| Tab Label | Brando | 10pt | 700 | 1.0 | 0.2pt | Bottom tab labels |
| Chip Label | Brando | 13pt | 500 | 1.0 | 0pt | Attribute pills, filter chips |
| Metadata | Brando | 13pt | 500 | 1.3 | 0pt | Dates, mile counts, chat timestamps |
| Counter Badge | Brando | 11pt | 700 | 1.0 | 0.2pt | The "1" / "5" badges on tab icons — set on yellow background |
| Compliments Hero | Brando | 22pt | 900 | 1.2 | -0.3pt | "She sent you a Compliment" |

### Principles
- **Heavy ladder for confidence**: Brando Bold (700) and Black (900) carry almost all headlines; the brand is loud.
- **Big display sizes**: 32-44pt headlines are common — Bumble wants you to read the words from across a room.
- **Tight tracking at display**: -0.8pt at 44pt falling to 0pt at body — pulls letters together for editorial impact.
- **Pure black on yellow**: When text sits on Bumble Yellow, use pure `#000000` for maximum contrast; everywhere else use Bumble Black (`#1F1F1F`).
- **Dynamic Type respected on body, bio, section headers**: Tab labels, badge counters, and chip labels are fixed (layout-sensitive).
- **Yellow CTAs use black text, always**: Never white text on yellow — contrast ratio drops below WCAG AA.

## 4. Component Stylings

### Buttons

**Primary CTA (Send / Continue / Make the first move)**
- Background: `#FFC629` (Bumble Yellow)
- Text: `#000000` (pure black, for WCAG AA on yellow)
- Padding: 16pt vertical, 32pt horizontal
- Corner radius: 28pt (capsule)
- Height: 56pt fixed
- Pressed: background `#F5B616` (Honey Deep), scale 0.97
- Shadow on hero CTAs: `rgba(255, 198, 41, 0.4) 0 8px 24px` — yellow glow shadow for the "Make the first move" hero
- Haptic: `.impactOccurred(.medium)` on Send; `.notificationOccurred(.success)` on match-completion

**Secondary Outline CTA**
- Background: transparent
- Border: 1.5pt `#1F1F1F`
- Text: `#1F1F1F`, Brando 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 28pt
- Pressed: background `#F5F5F5`

**Swipe Action Buttons (under the swipe card)**
- 5 circular buttons in a row: Rewind (small) / X Pass (medium) / Heart Yes (large, centered) / Star SuperSwipe (medium) / Bee (Compliment, small)
- Heart Yes is the hero: 64pt circle, `#FFC629` fill, black heart glyph at 28pt, `rgba(255, 198, 41, 0.5) 0 6px 16px` glow shadow
- X Pass: 56pt circle, white fill, 1.5pt `#1F1F1F` stroke, black X at 22pt
- Star SuperSwipe: 56pt circle, `#FFC629` fill, black star at 22pt — visually similar to Heart but smaller
- Bee Compliment: 56pt circle, white fill with 1.5pt `#FFC629` stroke, yellow-and-black bee glyph
- Rewind: 48pt circle, `#F5F5F5` fill, gray rewind icon
- Press animation: scale 0.9 on press; haptic varies by button (Heart = medium, Star = heavy, Bee = soft)

**The Honeycomb / Hive Button (BFF/Bizz mode toggle)**
- A hexagonal button — literally hex-shaped, not rectangular — used to switch between Date / BFF / Bizz modes
- Active mode hex: filled with that mode's color, slight inner shadow
- Inactive mode hexes: outlined only, mode glyph in muted color

### Cards & Containers

**Swipe Card (The Hero Component)**
- Width: full content width minus 24pt margins
- Aspect: 3:4 (slightly more portrait than square)
- Corner radius: 12pt (less rounded than Hinge — Bumble's photos are stronger-edged)
- Photo: full-bleed cover, with a 4-segment progress bar pinned at the top (each photo segment lights up in white as it advances; Stories-style)
- Bottom overlay: dark gradient from `rgba(0, 0, 0, 0)` to `rgba(0, 0, 0, 0.7)` over the bottom 30% of the card
- Name + age: pinned at the bottom-left over the gradient — "Sigrún, 28" in Brando 28pt Bold white
- Verified blue check: 18pt to the right of the name if verified
- Bio preview: 1 line of 16pt Medium white below the name; tap to expand into full vertical-scroll profile
- "i" info button: bottom-right 32pt circle, semi-transparent black fill, white "i" glyph

**Photo Progress Bar**
- 4 segments at the top of the photo, each 3pt tall, 4pt gap between segments
- Active segment: white `#FFFFFF`
- Inactive segment: `rgba(255, 255, 255, 0.4)`
- The bar is the Bumble photo navigation primitive — taps on left/right halves of the photo advance through

**Match Notification (the hexagon moment)**
- When two people match, a hex-cropped photo of the match appears at the top of the chat list with a yellow border ring
- Hex shape: a regular hexagon with 6:5 vertical-flat orientation (point-up)
- Border: 2pt `#FFC629` yellow stroke on the hex
- Subtle pulse on entry: scale 1.0 → 1.05 → 1.0 over 600ms
- Tap target: 64pt outer hex; the photo crops to fit inside

**Bee Tag / Verified Badge**
- A tiny hex-tag, ~20pt across, with a yellow `#FFC629` fill and a small bee glyph inside
- Sits on the top-right of profile photos in the chat list to indicate "Today's Bee" — the daily curated match
- Verified profiles get a blue circular check (not hex) — verified is distinct from "Today's Bee"

**Compliments Sheet (premium)**
- A bottom sheet with 24pt top corners
- Top strip: yellow honeycomb pattern background, 80pt tall, with three bee glyphs hovering
- "She sent you a Compliment" in Brando 22pt Black `#1F1F1F`
- The compliment text below in Brando 18pt Medium quoted in italic style
- Primary CTA: "Reply" pill (full yellow CTA spec)
- Compliments cost in-app currency (Beeline credits)

**Beeline (people who liked you, premium grid)**
- 2-col grid of profile thumbnails
- Each thumbnail: 4:5 aspect, 8pt corner radius, blurred preview if not subscribed
- Subscribed users see clear photos with a small yellow heart badge top-right
- Tap opens full profile; subscribers can see who liked them

### Navigation

**Top Header**
- Height: 56pt (slim header, no large-title pattern)
- Background: `#FFFFFF` on light, `#0F0F0F` on dark
- Title: Brando 24pt Black `#1F1F1F`, left-aligned, 20pt leading margin
- Trailing icons: Filters (3-slider glyph) on People, Pinned filter on Chats, Settings on Profile

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt `#E5E5E5` top divider; opaque (no blur — Bumble likes hard surfaces)
- Tabs: People (silhouettes), Hives (honeycomb), Matches (yellow heart), Chats (chat-bubble), Profile (person)
- Icon size: 24pt, outlined default / filled when active
- Active color: `#1F1F1F` (Bumble Black) — the active state is bold black, with a 4pt `#FFC629` indicator dot 4pt below the label
- Inactive: `#9C9C9C` (Mist)
- Labels: Brando 10pt weight 700, visible always
- Badge: `#FFC629` circle with `#000000` count text, sits top-right of the icon at 16pt diameter
- Tap haptic: `.selectionChanged()` on each switch

**The Hexagon Hive Logo**
- Bumble's hexagonal "B" logomark — a yellow hex with a stylized "B" bee inside
- Used on splash, the loading state (rotating hex animation), and as a watermark on the empty-states ("No matches yet — go make the first move!")
- Never rendered in any color other than `#FFC629` on white/dark or `#1F1F1F` outline on yellow

### Input Fields

**Chat Input ("Type a message" — the women-message-first input)**
- Background: `#F5F5F5`
- Border: none (focused state: 1.5pt `#1F1F1F`)
- Corner radius: 24pt (capsule)
- Padding: 14pt vertical, 20pt horizontal
- Placeholder: "Type your message…" in Brando 16pt Medium `#9C9C9C`
- Trailing send button: 40pt circle in `#FFC629` (Bumble Yellow), black arrow glyph at 18pt
- Disabled state (empty input): send circle drops to `#F5F5F5` with `#9C9C9C` arrow
- A small countdown timer chip sits above the input on new matches: "Your time to reply: 23h 14m" in Brando 13pt Bold `#1F1F1F` with a yellow accent border — Bumble's signature 24-hour expiry mechanic

**Text Field (Profile editing, Filters)**
- Background: `#FFFFFF`
- Border: 1pt `#E5E5E5` default, 1.5pt `#1F1F1F` focused
- Corner radius: 12pt
- Height: 52pt
- Label: floating — sits as placeholder at 16pt Medium `#9C9C9C` when empty; animates to 12pt Bold `#1F1F1F` at the top-left when focused or filled
- Text: Brando 16pt Medium `#1F1F1F`

**Filter Sliders (Distance, Age range)**
- Track: 4pt tall, `#E5E5E5` fill, rounded
- Active range: `#FFC629` Bumble Yellow fill
- Thumb: 24pt circle, white fill with `rgba(0,0,0,0.15) 0 2px 6px` shadow, 2pt `#FFC629` inner stroke
- Value labels: floating above the thumb in Brando 14pt Bold

### Distinctive Components

**It's a Match! Celebration**
- Full-screen takeover, `#FFC629` yellow canvas
- Two photos hex-cropped, side-by-side at the vertical center, 32pt apart, each ~120pt across, with 4pt white border on each hex
- A `#E94B7B` (Match Pink) heart icon centered between them, scale-bounces in
- "It's a Match!" in Brando 44pt Black `#1F1F1F` centered (-0.8pt tracking)
- Subline: "You and Sigrún want to chat" in Brando 16pt Medium `#1F1F1F`
- Primary CTA: "Send a Message" pill — pure `#FFFFFF` background, `#1F1F1F` text in Brando 18pt Bold, 56pt height
- Secondary action: "Keep Swiping" text button in `#1F1F1F` below
- Reminder banner: "She has 24 hours to make the first move" in Brando 13pt Medium centered

**Make the First Move (empty chat / opening screen)**
- The most iconic Bumble screen — an empty-state instructional moment
- Yellow `#FFC629` background, with the hex Bumble logo at the top
- Hero text: "Make the first move" in Brando 32pt Black `#1F1F1F` centered
- Subtitle: "She has 24 hours to start the conversation" in Brando 16pt Medium `#1F1F1F`
- 24-hour countdown timer in Brando 28pt Bold below
- Primary CTA: "Send a Move" — large pill, pure white with black text

**SuperSwipe / Star Send Animation**
- Single tap on the yellow star → entire screen flashes yellow `#FFC629` for 200ms then fades, with a star icon scale-bouncing from 1.0 → 1.4 → 1.0
- Haptic: `.notificationOccurred(.success)`
- The SuperSwipe banner appears on the recipient's view: "Sigrún SuperSwiped you" in yellow header

**24-Hour Countdown Chip**
- Sits at the top of new matched-but-unmessaged chats
- Pill: yellow `#FFC629` fill, black text "Your turn: 23h 14m" in Brando 13pt Bold
- 1pt inner stroke darker (`#F5B616`) for definition
- Counts down in real-time; when expired, the chip turns red `#D72638` and reads "Time's up — extend?"

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 16pt on lists; 24pt on swipe stack and hero screens
- Section vertical gap: 32pt between hero sections; 16pt between profile blocks

### Grid & Container
- Content width: full device width minus 16-24pt horizontal margins
- Swipe card: full-width minus 24pt margins, one card at a time stacked behind
- Profile detail: single column, full-bleed photo at top with overlay text
- Chats list: vertical list with hex-cropped avatar leading each row
- Beeline grid (premium): 2-col with 8pt gap

### Whitespace Philosophy
- **Yellow is the whitespace**: Where other apps use light grey breathing room, Bumble uses big blocks of yellow that themselves serve as visual rest
- **Big margins on hero screens**: 32pt vertical padding around "Make the first move" and "It's a Match!" — the screens are statements
- **Tight chat list**: 64pt rows with 16pt internal padding — Bumble wants you to see many conversations at once

### Border Radius Scale
- Square (0pt): rarely used
- Small (4pt): toast notifications
- Standard (8pt): Beeline grid photos, small chips
- Medium (12pt): swipe cards, text fields, settings rows
- Large (20pt): modal sheets, premium hero modules
- Capsule (28pt): primary CTAs
- Full Pill (500pt): chips, chat input, attribute tags
- Circle (50%): swipe action buttons, avatar circles, badges, indicator dots
- **Hexagon**: literal hex shape for the brand mark, match notification photo crops, mode toggle buttons — clip-path or SVG mask

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Yellow surfaces, chat backgrounds |
| Card (Level 1) | `rgba(0, 0, 0, 0.08) 0 2px 8px` | Profile cards, chat list rows on press |
| Floating (Level 2) | `rgba(0, 0, 0, 0.12) 0 4px 16px` | Swipe card top of stack, secondary modals |
| Hero CTA Glow | `rgba(255, 198, 41, 0.4) 0 8px 24px` | The yellow primary CTA on hero screens — the brand's signature glow |
| Heart Button Glow | `rgba(255, 198, 41, 0.5) 0 6px 16px` | The big yellow heart action button — yellow halo |
| Overlay (Level 3) | `rgba(0, 0, 0, 0.16) 0 8px 32px` | Compliments sheet, premium modals |
| Sheet (Level 4) | `rgba(0, 0, 0, 0.24) 0 -12px 32px` | Bottom sheets, "It's a Match" entry |

**Shadow Philosophy**: Bumble is not subtle. The yellow glow shadow on the heart button and on hero CTAs is a brand signature — a soft yellow halo that says "this is the action". Shadows in chrome are larger and slightly more opaque than Hinge or Airbnb (0.12-0.16 max, 8-32pt blur). Hard surfaces, big confidence.

### Motion
- **Swipe gesture**: spring-tracked drag with rubber-band; commit-threshold ~30% of width; on commit, the card spins slightly (rotation 8°) and slides off-screen in 350ms
- **Yes/No commit animation**: confetti-like burst of small yellow hexagons fall from the top of the screen for 800ms
- **Heart button tap**: scale 1.0 → 1.2 → 1.0 over 250ms spring (damping 0.6); the yellow glow halo intensifies, then the card commits
- **Star (SuperSwipe) tap**: full-screen yellow flash for 200ms, paired with `.notificationOccurred(.success)`
- **Bee (Compliment) tap**: scale-up the bee glyph 1.0 → 1.3 → 1.0 over 400ms with a wing-flap rotation, opens the Compliment composer
- **Match celebration entry**: photos slide in from offscreen left/right to center, the pink heart pops in via scale-bounce, "It's a Match!" type animates up from below with letter-by-letter cascade
- **24-hour timer pulse**: subtle 1500ms opacity 0.95 → 1.0 → 0.95 pulse on the countdown chip
- **Hex loading state**: 6 yellow hexes arranged in a honeycomb pattern, rotating around the center at 1200ms per cycle
- **Tab switch**: 200ms ease; indicator dot fades in 4pt below the label; `.selectionChanged()` haptic

## 7. Do's and Don'ts

### Do
- Use Bumble Yellow (`#FFC629`) as the dominant action color — it's the brand
- Pair yellow with pure black (`#000000`) for text on yellow surfaces — WCAG AA requires it
- Use Brando weights 500/700/900 — the brand is heavy and confident
- Render the heart action button with a yellow glow shadow — `rgba(255, 198, 41, 0.5) 0 6px 16px`
- Use the hexagon shape for the brand mark, match notification photo crops, and mode toggle buttons
- Use the 24-hour countdown chip on new matches — it's the core mechanic of the app
- Use the photo progress bar at the top of the swipe card (4 segments, Stories-style)
- Make the "It's a Match!" celebration yellow-canvas full-screen with hex-cropped photos
- Use `#1F1F1F` warm Bumble Black for primary text on white surfaces, not pure black
- Add big confident shadows on hero CTAs — the yellow glow is intentional
- Use Match Pink (`#E94B7B`) only on the It's a Match heart — nowhere else
- Switch all yellow chrome to the mode color (teal `#11AAA8` BFF / orange `#FF8000` Bizz) when in those modes

### Don't
- Don't use white text on Bumble Yellow — contrast fails WCAG; always pure black
- Don't use Bumble Yellow for body text — yellow is a verb, never type
- Don't soften the photo aspect to 4:5 portrait (that's Hinge) — Bumble uses 3:4
- Don't replace the hexagon with a circle on match notifications — the hex is the brand spatial signature
- Don't use Brando Light or Thin — the brand doesn't have light weights
- Don't blur the heart-button glow into nothing — the yellow halo is a key visual; keep it visible
- Don't put yellow chrome on BFF mode (use teal) or Bizz mode (use orange) — the mode color is sacred to that product
- Don't render the bee glyph anywhere except the Compliments button and the "Today's Bee" badge
- Don't use rounded card photos with radius > 12pt — Bumble's cards have firmer edges than Hinge/Tinder
- Don't skip the 24-hour countdown on new chats — it's the core mechanic; design must surface it
- Don't use pure black (`#000000`) for body text on white — Bumble Black `#1F1F1F` is warmer
- Don't use cool greys — Bumble's mid-greys are neutral but the deep ones are warm-toned

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Swipe card aspect shifts 3:4 → 1:1.2 for compactness; action buttons drop from 64pt to 56pt diameter |
| iPhone 13/14/15 | 390pt | Standard 3:4 card, 24pt margins, 64pt heart button |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; header drops to 52pt to make room |
| iPhone 15/16 Pro Max | 430pt | Larger cards, action button row gains slight extra spacing |
| iPad | 768pt+ | 2-col swipe view with side stack browser; chat list moves to a sidebar; main view becomes the conversation |

### Dynamic Type
- Match Hero, Display, Screen Title, Body, Bio: full scale
- Tab labels, badge counters, chip labels: fixed (layout-sensitive)
- The 24-hour countdown chip: fixed (tabular numerals)

### Orientation
- All swipe surfaces: portrait-locked
- Photo lightbox on profile: rotates to landscape full-bleed
- Match celebration: portrait only — confetti choreography is portrait-tuned

### Touch Targets
- Heart action button: 64pt diameter, 80pt full hit area
- Other swipe action buttons: 56pt
- Tab bar icons: 24pt icon, 56pt full row
- CTAs: 56pt height, full width minus 24pt margins
- 24-hour chip: 32pt tall, full-width minus 32pt margins

### Safe Area Handling
- Top: safe area honored — the 56pt header sits below the status bar/dynamic island
- Bottom: chat input pins above home indicator; tab bar pins above home indicator on other screens
- Sides: 16-24pt content insets depending on screen

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface 1: `#F5F5F5`
- Divider: `#E5E5E5`
- Bumble Yellow (primary): `#FFC629`
- Honey Deep (pressed): `#F5B616`
- Yellow Light (soft chips): `#FFE9A1`
- Bumble Black (primary text): `#1F1F1F`
- Bumble Slate (secondary text): `#5A5A5A`
- Bumble Mist (tertiary): `#9C9C9C`
- Pure Black (on yellow): `#000000`
- Match Pink (Match heart only): `#E94B7B`
- BFF Teal: `#11AAA8`
- Bizz Orange: `#FF8000`
- Verified Blue: `#0066FF`
- Error Red: `#D72638`
- Success Green: `#00A86B`
- Dark canvas: `#0F0F0F`

### Example Component Prompts
- "Create a SwiftUI Bumble swipe card: full-width minus 24pt margins, 3:4 aspect, 12pt corner radius. Full-bleed cover photo with a 4-segment progress bar pinned at the top in 3pt height, 4pt gap, active segment white and inactive `rgba(255,255,255,0.4)`. Bottom dark gradient overlay from transparent to `rgba(0,0,0,0.7)` over the bottom 30%. 'Sigrún, 28' in Brando 28pt Bold white pinned bottom-left, with a small Verified Blue check trailing the name if verified. An 'i' info button bottom-right — 32pt circle, semi-transparent black, white 'i'. The card is swipeable: drag past 30% of width to commit, then it rotates 8° and slides off in 350ms."
- "Build the Bumble swipe action row: 5 circular buttons in a horizontal row below the card. From left: Rewind (48pt, `#F5F5F5` fill, gray glyph), X Pass (56pt, white fill with 1.5pt `#1F1F1F` stroke, black X), Heart Yes (64pt, `#FFC629` Bumble Yellow fill, 28pt black heart glyph, with a `rgba(255,198,41,0.5) 0 6px 16px` yellow glow shadow), Star SuperSwipe (56pt, `#FFC629` fill, black star), Bee Compliment (56pt, white fill with 1.5pt `#FFC629` stroke, yellow-and-black bee glyph). Press animation: scale 0.9 on press. Heart triggers `.impactOccurred(.medium)`; SuperSwipe `.notificationOccurred(.success)`; Bee `.impactOccurred(.soft)`."
- "Design the Bumble 'It's a Match!' celebration: full-screen `#FFC629` yellow canvas. Two photos hex-cropped (regular hexagon, point-up orientation) side-by-side at the vertical center, 32pt apart, each 120pt across with a 4pt white border on the hex. A 48pt Match Pink (`#E94B7B`) heart icon centered between them, scale-bounces in 1.0 → 1.4 → 1.0 over 400ms. Below the hexes: 'It's a Match!' in Brando 44pt Black `#1F1F1F` with -0.8pt letter spacing, centered. Subline: 'You and Sigrún want to chat' in Brando 16pt Medium `#1F1F1F`. A reminder banner below: 'She has 24 hours to make the first move' in Brando 13pt Medium centered. Primary CTA 'Send a Message' pinned at the bottom — white background, `#1F1F1F` text in Brando 18pt Bold, 56pt tall pill. Tertiary 'Keep Swiping' text button below in `#1F1F1F`. Entry haptic: `.notificationOccurred(.success)` with a `.impactOccurred(.medium)` 200ms later."
- "Create the Bumble 24-hour countdown chip: full-width capsule sitting at the top of new matched-but-unmessaged chats. Background `#FFC629` Bumble Yellow with a 1pt `#F5B616` inner stroke for definition. Text 'Your turn: 23h 14m' in Brando 13pt Bold `#000000` (pure black for WCAG on yellow). 32pt tall, 16pt horizontal padding. A subtle 1500ms opacity 0.95 → 1.0 → 0.95 pulse animation. When expired, the chip transitions to `#D72638` Error Red and reads 'Time's up — extend?' with the same Brando 13pt Bold."
- "Build the Bumble chat input bar: pinned above the keyboard. `#F5F5F5` Surface 1 background capsule with 24pt radius, 14pt vertical / 20pt horizontal padding. Placeholder 'Type your message…' in Brando 16pt Medium `#9C9C9C`. Trailing send button — 40pt circle in `#FFC629` Bumble Yellow with a black arrow glyph at 18pt. Disabled state when input is empty: send circle drops to `#F5F5F5` with `#9C9C9C` arrow. Send haptic: `.impactOccurred(.medium)`. The countdown chip from above sits 8pt above this input for new matches."

### Iteration Guide
1. Bumble Yellow `#FFC629` is the dominant brand color — splash, CTAs, tab indicator, match notification, heart button
2. Pair yellow with pure black (`#000000`) for text — WCAG AA requires it
3. Brando weights 500/700/900 only — the brand is heavy and confident
4. Body text on white: Bumble Black `#1F1F1F` (warm), not `#000000`
5. Heart button gets a yellow glow shadow: `rgba(255, 198, 41, 0.5) 0 6px 16px`
6. Hexagon for the brand mark, match notification photos, mode toggles — never replace with circle
7. The 24-hour countdown chip is core mechanic — surface it prominently on new matches
8. Mode switching (Date/BFF/Bizz) replaces all yellow chrome with the mode color, keeping hex shape constant
9. Match celebration: yellow full-screen with hex-cropped photos and a pink heart between
10. Dark mode: warm `#0F0F0F` canvas with `#F2F2F2` text; Bumble Yellow stays identical for OLED contrast
