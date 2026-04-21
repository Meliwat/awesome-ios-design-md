# Design System Inspiration of Tinder (iOS)

## 1. Visual Theme & Atmosphere

Tinder's iOS app is built around a single, unforgettable gesture: the swipe. Every primary screen is really just a stage for the swipeable card — a 3:4 portrait photo that takes up roughly 80% of the viewport, adorned with a name, age, and a trio of subtle overlays, floating on a clean canvas. The visual language is pure gravity: the card is the object, the five action buttons below it are the verbs, and everything else is chrome that steps aside. This is the rare app where the UI doesn't frame the content — the content *is* the UI, and every design decision is in service of the swipe feeling fast, tactile, and slightly dangerous.

The accent is a horizontal gradient from Tinder Pink (`#FD267A`) to Tinder Orange (`#FF6036`) — the exact gradient of the famous flame logo. This gradient shows up exactly three places: on the "Match" screen (the entire background goes gradient when two users match), on the primary "subscribe" CTA for Tinder Gold/Platinum, and in micro-moments like the flame icon in the tab bar and the heart stamp overlay on a right-swipe. Beyond the gradient, the app is ruthlessly disciplined: Pink (`#FD267A`) alone marks the "Like" action, Blue (`#5D8DF1`) marks "Super Like", Purple (`#A952FF`) marks "Boost", Gold (`#FFBD3B`) marks "Rewind", and Green marks new-match glow states. These five colors are the verbs, and the app never uses them decoratively. The canvas itself is stark white (`#FFFFFF`) in light mode, `#121212` in dark — letting the photos do all the work.

Typography is Tinder Sans (proprietary, 2021) — a humanist sans with slightly condensed proportions, optimized for readability at card-overlay sizes. Before 2021, Tinder used Proxima Nova, and many legacy surfaces still reflect that family's geometry. The hierarchy on the swipe card itself is intentionally minimal: the person's name is 28pt weight 700 in white, with a comma and a lighter 24pt weight 400 age right after ("Jamie, 27"). Below that, a single line of metadata (location, distance, occupation) at 14pt weight 400 with 70% white opacity. The "It's a Match!" screen uses an entirely different type register — 48pt script-feeling display text that feels celebratory, because the whole point of the app is getting here.

**Key Characteristics:**
- Swipe cards — 3:4 profile photos full-bleed, name + age overlaid bottom-left, info "ⓘ" button bottom-right
- Pink-to-orange brand gradient (`#FD267A → #FF6036`) for Match screens and premium upsells
- Stamp overlays during swipe: "LIKE" (green rotated), "NOPE" (red rotated), "SUPER LIKE" (blue rotated)
- Five action buttons below the card: Rewind (gold) | Nope (red X) | Super Like (blue star) | Like (green heart) | Boost (purple lightning)
- Tinder Sans font (2021+), Proxima Nova legacy fallback
- Circular action buttons with colored SF Symbols — the only place color appears in chrome
- Match screen: full pink→orange gradient, "It's a Match!" in script display type, both profiles side-by-side
- Chat bubbles: white sender / pink gradient receiver (matched user) — the visual "this is your match" continues
- Tab bar: Flame (swipe) | Star (top picks) | Chat bubble | Profile — icons only, no labels, spartan
- Haptics on swipe threshold, match reveal, and super-like — the gesture *feels* like a slot machine

## 2. Color Palette & Roles

### Primary (Brand Gradient)
- **Tinder Pink** (`#FD267A`): Primary brand anchor, Like heart button, gradient start.
- **Tinder Orange** (`#FF6036`): Gradient terminus, flame logo secondary.
- **Brand Gradient**: horizontal linear `#FD267A → #FF6036` — used on Match screen, premium CTAs, and the flame logo itself.

### Action Colors (The 5 Verbs)
- **Like Pink** (`#FD267A`): Right-swipe action, heart button, "LIKE" stamp green? — NO, stamp is green for Like; the button glyph is pink.
  - **Like Stamp Green** (`#00D68F`): The rotated "LIKE" stamp overlay on right-swipe; echoes "yes" / "match" semantics.
- **Nope Red** (`#FF4458`): X button background, "NOPE" stamp overlay, swipe-left haptic.
- **Super Like Blue** (`#5D8DF1`): Star button, "SUPER LIKE" stamp, swipe-up action, premium badge.
- **Boost Purple** (`#A952FF`): Lightning button (promote profile for 30 min), premium feature highlight.
- **Rewind Gold** (`#FFBD3B`): Clockwise-arrow button (undo last swipe), flame-gold moments.

### Canvas & Text (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary background.
- **Surface Muted** (`#F5F5F5`): Card backgrounds in profile view, chat background.
- **Surface Tint** (`#FAFAFA`): Sectional fills, table row background.
- **Divider** (`#E5E5E5`): Hairline dividers.
- **Text Primary** (`#424242`): Primary body text (note: Tinder uses a softer dark gray, not pure black).
- **Text Secondary** (`#737373`): Metadata, timestamps, helper text.
- **Text Tertiary** (`#9E9E9E`): Disabled state, placeholder.

### Dark Mode
- **Dark Canvas** (`#121212`): Primary dark canvas.
- **Dark Surface 1** (`#1D1D1D`): Card background on dark.
- **Dark Surface 2** (`#2A2A2A`): Elevated surfaces on dark.
- **Dark Divider** (`#3A3A3A`).
- **Dark Text Primary** (`#FFFFFF`).
- **Dark Text Secondary** (`#B8B8B8`).

### Semantic
- **Match Glow Green** (`#00D68F`): "New Match!" glow animation, unread-match badge dot.
- **Alert Red** (`#FF4458`): Error, form validation, destructive.
- **Online Dot Green** (`#00D68F`): "Recently active" dot in chat list.
- **Verified Blue** (`#29B0FF`): Blue-check verified profile badge.

### Profile Card Overlay
- **Card Gradient Bottom** (`rgba(0,0,0,0.0) → rgba(0,0,0,0.7)`): Vertical gradient at bottom ~30% of card to ensure name/age/meta readability over any photo.

## 3. Typography Rules

### Font Family
- **Primary (2021+)**: `Tinder Sans` (proprietary, humanist sans) — used across profile cards, CTAs, body, UI
- **Legacy (pre-2021)**: `Proxima Nova` (many older surfaces still reflect it)
- **Match Screen Display**: `Tinder Sans Display` weight 800 italic, used only for "It's a Match!" and hero moments
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Proxima Nova', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Noto Sans family fallback

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Match Hero | Tinder Sans Display | 48pt | 800 italic | 1.1 | -0.5pt | "It's a Match!" on match screen |
| Screen Title | Tinder Sans | 28pt | 700 | 1.2 | -0.3pt | "Matches", "Messages" hero |
| Profile Name (Card) | Tinder Sans | 28pt | 700 | 1.15 | -0.3pt | Name on swipe card |
| Profile Age (inline) | Tinder Sans | 24pt | 400 | 1.15 | 0pt | Age follows name after comma |
| Section Header | Tinder Sans | 20pt | 700 | 1.2 | -0.2pt | "About me", "Photos" |
| Button (Primary) | Tinder Sans | 16pt | 700 | 1.0 | 0.2pt | "Get Tinder Gold", "Send Message" |
| Button (Secondary) | Tinder Sans | 15pt | 500 | 1.0 | 0pt | Text links, outline buttons |
| Body | Tinder Sans | 15pt | 400 | 1.45 | 0pt | Bio, "About me" paragraph |
| Card Meta | Tinder Sans | 14pt | 400 | 1.3 | 0pt | "2 miles away · Software Engineer" |
| Chat Message | Tinder Sans | 15pt | 400 | 1.35 | 0pt | Bubble text |
| Chat Timestamp | Tinder Sans | 11pt | 400 | 1.2 | 0pt | Below bubble, gray |
| Stamp (LIKE/NOPE) | Tinder Sans | 36pt | 800 italic | 1.0 | 2pt | Rotated swipe stamp overlay |
| Tab Label (none) | — | — | — | — | — | Tab bar is icon-only, no labels |
| Input Placeholder | Tinder Sans | 16pt | 400 | 1.0 | 0pt | Search input, message input |
| Notification | Tinder Sans | 13pt | 500 | 1.3 | 0pt | Toast, snackbar messages |

### Principles
- **Two type moments**: Small UI is in Tinder Sans; the match screen breaks the rules with italic display type
- **Weights concentrated at 400 / 500 / 700 / 800-italic**: 800 italic exclusively for the match screen
- **Name/age formula**: "Jamie, 27" — name at 28pt w700, comma, age at 24pt w400, no other ornamentation
- **Tight tracking on titles**: -0.3pt on 20pt+
- **Stamp type is rotated 15°**: The "LIKE" / "NOPE" / "SUPER LIKE" swipe stamps sit at a slight rotation
- **No italics in body**: Italic is reserved for the match-hero moment

## 4. Component Stylings

### Buttons

**Five Action Buttons (Below Swipe Card)**
- All are circular, ranging from 48pt (outer: Rewind, Boost) to 64pt (outer: Nope, Like) to 40pt (center: Super Like)
- Stacked horizontally: `[Rewind 48pt] [Nope 64pt] [Super Like 40pt] [Like 64pt] [Boost 48pt]`
- Wait — standard Tinder arrangement: `[Rewind 48] [Nope 56] [Super Like 40] [Like 56] [Boost 48]`
- Background: `#FFFFFF` with 2pt colored stroke ring matching action
- Shadow: `rgba(0,0,0,0.1) 0 2px 8px`
- Glyph: 24pt colored SF Symbol centered
  - Rewind: `arrow.counterclockwise` in `#FFBD3B` gold
  - Nope: `xmark` in `#FF4458` red
  - Super Like: `star.fill` in `#5D8DF1` blue
  - Like: `heart.fill` in `#00D68F` green
  - Boost: `bolt.fill` in `#A952FF` purple
- Pressed: scale 0.92 spring + medium haptic, glyph fills the ring entirely for a brief 150ms

**Primary Pill CTA ("Get Tinder Gold", "Send Like")**
- Background: pink→orange brand gradient `#FD267A → #FF6036`
- Text: `#FFFFFF`, Tinder Sans 16pt w700 with 0.2pt tracking
- Padding: 14pt vertical, 32pt horizontal
- Corner radius: 32pt (full pill)
- Height: 52pt
- Pressed: scale 0.97 + gradient darkens 10%, medium haptic
- Shadow: `rgba(253,38,122,0.3) 0 4px 16px`

**Secondary Outline Button**
- Background: transparent
- Border: 1.5pt `#424242` (light mode) / `#FFFFFF` (dark mode)
- Text: `#424242` / `#FFFFFF`, Tinder Sans 15pt w500
- Height: 44pt, corner radius 22pt (pill)
- Pressed: border color darkens + light haptic

**Icon Button (Chat Header)**
- Size: 44pt
- Icon: 22pt, `#737373` default / `#FD267A` on interaction
- Background: transparent
- Used for: back chevron, profile, menu

### Cards & Containers

**Swipe Card**
- Width: full screen minus 16pt margins
- Aspect: 3:4 (e.g., 358×478pt on iPhone 15)
- Corner radius: 16pt
- Shadow: `rgba(0,0,0,0.15) 0 4px 16px`
- Content: full-bleed photo
- Photo pagination: tap left/right edge to go prev/next photo; thin `#FFFFFF` dashes at top of card count photos, with a subtle white-filled active segment
- Bottom gradient: `rgba(0,0,0,0.0) → rgba(0,0,0,0.7)` over bottom 30% for text legibility
- Name + age row: name 28pt w700 white + ", age 24pt w400 white" (inline)
- Verified badge: 18pt blue `checkmark.seal.fill` next to name if verified
- Meta row: "2 miles away · Software Engineer" in Tinder Sans 14pt w400 `rgba(255,255,255,0.9)`
- Info button (ⓘ): bottom-right corner, 32pt white circle with `info` glyph in `#424242`

**Profile Card (Expanded Detail)**
- Appears when user taps ⓘ or scrolls up on a card
- Full-screen sheet with same photo carousel at top
- Below: bio text, prompts (e.g., "My ideal Saturday: 🌅"), Instagram/Spotify sections
- Corner radius: 24pt on the sheet
- Close button: top-right, 32pt white circle with `xmark` glyph

**Match Screen**
- Full-screen takeover with pink→orange gradient background `#FD267A → #FF6036`
- Top: "It's a Match!" in Tinder Sans Display 48pt w800 italic white
- Subtitle: "You and [Name] liked each other" in Tinder Sans 16pt w400 white 80%
- Two circular profile photos (120pt each) side-by-side, with 20pt gap
- CTA row below: "Send Message" pill (white bg, gradient text) | "Keep Playing" outline (white border, white text)
- Celebratory confetti-like particle animation on entry (300ms)
- Success haptic + optional sound

**Chat Bubble**
- Incoming (from matched user): background pink→orange gradient (or solid `#FD267A`), text white, 20pt radius (more bubbly on sender-side corner)
- Outgoing (yours): background `#F5F5F5`, text `#424242`, 20pt radius with squared corner bottom-right
- Max width: 75% of screen
- Padding: 10pt vertical, 14pt horizontal
- Timestamp below: 11pt w400 `#9E9E9E`
- GIF / Sticker messages: no bubble, just the media with rounded corners
- Long-press: shows reactions (6 emoji row)

**Conversation List Row**
- Height: 72pt
- Leading: 56pt circular profile photo with optional `#00D68F` online-dot overlay (12pt circle, 2pt white ring)
- Title: Tinder Sans 16pt w700 `#424242` (name)
- Snippet: Tinder Sans 14pt w400 `#737373`, 1-line clamp
- Trailing: timestamp Tinder Sans 12pt w400 `#9E9E9E` + optional unread dot (10pt pink circle)
- Pressed: `#FAFAFA`

### Navigation

**Top Nav (Swipe Screen)**
- Height: 56pt + safe area
- Background: `#FFFFFF` (light) / `#121212` (dark), transparent when swipe card is scrolled
- Leading: 32pt flame logo (pink→orange gradient filled)
- Trailing: 32pt settings gear `#424242`, then 32pt boost lightning `#A952FF` (premium chip)
- No title text — logo is the anchor

**Bottom Tab Bar**
- Height: 64pt + safe area (taller than typical because icons are larger)
- Background: `#FFFFFF` with 0.5pt top border `#E5E5E5`
- Tabs: Flame (swipe) | Star (Top Picks / Likes You) | Chat bubble | Profile
- Icons: 28pt (bigger than typical, no labels)
- Active: pink→orange gradient filled icon
- Inactive: `#9E9E9E` outline icon
- No labels — icon-only tab bar

### Input Fields

**Search Input (Conversations)**
- Background: `#F5F5F5`
- Height: 40pt
- Leading: 16pt `magnifyingglass` `#737373`
- Placeholder: "Search matches", Tinder Sans 16pt w400 `#9E9E9E`
- Corner radius: 20pt (pill)
- Focus: no border change

**Message Input (Chat)**
- Height: 44pt
- Background: `#F5F5F5`, corner radius 22pt (pill)
- Leading: GIF picker icon 22pt
- Placeholder: "Say something...", Tinder Sans 15pt w400 `#9E9E9E`
- Trailing: when text entered, send button appears as 36pt pink→orange gradient circle with `arrow.up` white inside

### Distinctive Components

**Swipe Stamps**
- During a swipe gesture, an overlay "stamp" appears at the top of the card, rotated 15°, with opacity tied to swipe progress
- **LIKE**: green `#00D68F`, Tinder Sans 36pt w800 italic, 4pt border, rotated -15° (right side of card)
- **NOPE**: red `#FF4458`, Tinder Sans 36pt w800 italic, 4pt border, rotated 15° (left side of card)
- **SUPER LIKE**: blue `#5D8DF1`, Tinder Sans 32pt w800 italic, 4pt border, centered top, paired with a blue star burst animation
- Opacity: 0 → 1 as swipe distance reaches 40% of threshold; snaps to full opacity past 60%

**Photo Paginator (Top of Card)**
- Thin `#FFFFFF` line at top of card, divided into N equal segments (one per photo)
- Inactive segment: `rgba(255,255,255,0.4)` filled
- Active segment: `rgba(255,255,255,1.0)` filled
- Segment height: 3pt; gap between: 3pt
- Padding: 8pt from top, 16pt left/right

**New Match Glow**
- On the conversation list, an unread new match gets a pulsing `#00D68F` green ring around the avatar
- Ring: 2pt stroke expanding 1.0 → 1.2 scale over 1.5s, opacity 1.0 → 0.0, infinite loop

**Boost Activated Chip**
- Top-right corner pill when Boost is active
- Background: purple→pink gradient `#A952FF → #FD267A`
- Text: white, Tinder Sans 12pt w700 "⚡ Boost Active · 25m"
- Corner radius: 16pt
- Countdown updates live

**Tinder Gold / Platinum Hero**
- Full-screen modal with gold gradient background `#FFBD3B → #FF6036`
- Hero flame logo (oversized, 120pt) centered
- Feature list with checkmarks in gold
- Sticky CTA at bottom: "Continue" pill with brand gradient

**Top Picks Card (Curated Daily)**
- Same 3:4 card but with gold glow around the border
- "Top Pick" badge top-left corner: gold `#FFBD3B` pill with black text
- Limited to premium users

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard margin: 16pt horizontal
- Card stack: centered in viewport, 16pt margins from edges, 24pt above action buttons
- Action buttons: centered row, 12pt gap between, 32pt below card

### Grid & Container
- Swipe stage: single card centered, subsequent cards peek behind (slight scale offset, 0.95)
- Profile detail sheet: single column, 16pt horizontal padding, sections with 24pt gaps
- Chat: full-width messages, 16pt horizontal padding, bubbles max 75%

### Whitespace Philosophy
- **The card breathes**: 16pt margins on sides, 20pt above (below nav), 32pt below (above action buttons)
- **Action buttons have room**: 32pt of canvas around them — they're meant to look iconic, like a remote control
- **Match screen is all space**: gradient fills everything, photos and text centered with generous negative space
- **Chat is dense but cozy**: bubbles pack tight but have 12pt gaps between messages

### Border Radius Scale
- Square (0pt): Rare, only for sharp pattern tiles
- Subtle (4pt): Photo pagination segments
- Standard (12pt): Inner cards, small buttons
- Card (16pt): Swipe card, bottom sheet
- Comfortable (20pt): Chat bubbles, modal sheets
- Pill (22-32pt): Secondary buttons, search inputs
- Full Pill (500pt): Primary CTAs, message input
- Circle (50%): Action buttons, profile photos, tab icons, send button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, chat backgrounds |
| Hairline (Level 1) | 1pt `#E5E5E5` border | Outline buttons, input borders (rare) |
| Card (Level 2) | `rgba(0,0,0,0.15) 0 4px 16px` | The swipe card — the primary elevated object |
| Action Button (Level 3) | `rgba(0,0,0,0.1) 0 2px 8px` | Five circular action buttons below the card |
| Primary CTA Glow (Level 4) | `rgba(253,38,122,0.3) 0 4px 16px` | Pink→orange gradient buttons (branded shadow) |
| Match Screen (Level 5) | Full-screen gradient | Match takeover — no shadows, just saturated color |
| Sheet (Level 6) | `rgba(0,0,0,0.2) 0 -4px 20px` | Profile detail sheet rising from bottom |
| Dim Overlay | `rgba(0,0,0,0.6)` | Behind modal sheets and match screen entry |

**Shadow Philosophy**: The swipe card floats — it's the object of desire, and the 15% black shadow at 16pt blur is what sells the "you can pick it up" feeling. Action buttons get a lighter 10% shadow to sit as a second layer below the card. Primary CTAs get a *colored* shadow (pink glow) instead of a black shadow, because the gradient is the identity and a black shadow would dull it. The match screen abandons shadows entirely — the gradient is the whole event.

### Motion
- **Swipe drag**: card follows finger 1:1 with slight rotation based on horizontal velocity (-15° to 15°)
- **Swipe threshold**: at 40% horizontal displacement, stamp overlay fades in; at 100% it locks and the card animates off-screen at 600pt/s
- **Swipe snap-back**: if threshold not reached, spring back with damping 0.7 over 400ms, light haptic
- **Like / Nope commit**: card flies off-screen 400ms ease-out, next card scales from 0.95 to 1.0 over 250ms, medium haptic at commit
- **Super Like swipe up**: blue star burst particle animation (300ms), success haptic, card flies up off-screen
- **Match reveal**: full-screen gradient fade-in 300ms, confetti particle animation over 800ms, success + medium haptic combo
- **Action button press**: scale 0.92 spring damping 0.5, glyph fills the ring briefly (150ms), medium haptic
- **Photo paginator tap**: 120ms cross-fade between photos, no haptic (lightweight)
- **New match glow**: infinite 1.5s ease-in-out green ring pulse on avatar
- **Boost activation**: purple→pink gradient sweep across the card stack (500ms), strong haptic

## 7. Do's and Don'ts

### Do
- Center the swipe card in the viewport with 16pt margins and 32pt of breathing room below it
- Use the brand gradient `#FD267A → #FF6036` on Match screens and premium CTAs — never elsewhere
- Assign one color per action: Gold (Rewind), Red (Nope), Blue (Super Like), Green (Like), Purple (Boost)
- Render LIKE / NOPE / SUPER LIKE stamps rotated 15° with 4pt colored borders in italic display type
- Show the photo paginator at the top of the card as thin white segments
- Use "Name, age" formula (28pt w700 + 24pt w400 after comma)
- Trigger medium haptic on swipe commit and action button tap; success haptic on match
- Keep the tab bar icon-only (Flame / Star / Chat / Profile) — no labels
- Use the softer `#424242` for body text in light mode (not pure black)
- On match, let the entire screen go pink→orange gradient — it should feel like a celebration

### Don't
- Don't change the swipe card aspect ratio — 3:4 portrait is load-bearing to the gesture
- Don't add text labels to the tab bar — icons only
- Don't use the brand gradient on every button — reserved for Match screens and premium
- Don't swap the action button colors — the 5-verb color system is the UI language
- Don't use pure black (`#000000`) for body text — `#424242` is softer and on-brand
- Don't straighten the LIKE / NOPE stamps — they must be rotated
- Don't add decorative colors to chat bubbles beyond sender/receiver — the gradient is the sender identity
- Don't hide the flame logo in the top-left of the swipe screen — it's the visual anchor
- Don't animate swipe commits too slow — it must feel snappy (400ms max off-screen)
- Don't use underlines on links — color + weight signal interactivity

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card height reduces by 60pt, action buttons shrink proportionally |
| iPhone 13/14/15 | 390pt | Standard 358×478pt card |
| iPhone 15/16 Pro | 393pt | Dynamic Island pushes nav 8pt lower, card unchanged |
| iPhone 15/16 Pro Max | 430pt | Card scales to 398×530pt, profile detail gets more breathing room |
| iPad | 768pt+ | Card caps at 420pt wide, centered with generous whitespace around |

### Dynamic Type
- Profile name/age, body, meta: full scale
- Match hero "It's a Match!": scales to 56pt max then truncates
- Stamp type (LIKE/NOPE): fixed 36pt (layout-sensitive rotation)
- Tab icons: fixed 28pt (visual consistency)
- Chat messages: full scale with max multiplier 1.4

### Orientation
- Swipe / Chat / Profile: **portrait-locked** (the card gesture is designed for portrait)
- Match screen: **portrait-locked**
- No landscape support — the card is the product

### Touch Targets
- Action buttons: 48-64pt (larger than typical because they're mission-critical)
- Swipe card: full-card drag area (300pt+ diagonal)
- Photo paginator tap zones: 50% of card width (left half = previous photo, right half = next)
- Chat send button: 36pt circle with 44pt effective hit
- Tab icons: 28pt icon inside 44pt hit

### Safe Area Handling
- Top: safe area honored on nav (except swipe screen extends under with transparent nav)
- Bottom: action buttons respect home indicator with 24pt padding above
- Sides: 16pt content insets everywhere

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface muted: `#F5F5F5`
- Text primary: `#424242`
- Text secondary: `#737373`
- Tinder Pink (Like): `#FD267A`
- Tinder Orange: `#FF6036`
- Nope Red: `#FF4458`
- Super Like Blue: `#5D8DF1`
- Boost Purple: `#A952FF`
- Rewind Gold: `#FFBD3B`
- Like Stamp Green: `#00D68F`
- Verified Blue: `#29B0FF`
- Brand Gradient: `linear-gradient(90deg, #FD267A 0%, #FF6036 100%)`

### Example Component Prompts
- "Create a SwiftUI Tinder swipe card: 3:4 aspect ratio, full-bleed photo, 16pt corner radius, shadow `rgba(0,0,0,0.15) 0 4px 16px`. Bottom 30% vertical gradient `rgba(0,0,0,0.0) → rgba(0,0,0,0.7)` for legibility. Name 'Jamie' in Tinder Sans 28pt w700 white + comma + age '27' in Tinder Sans 24pt w400 white. Meta row '2 miles away · Software Engineer' in Tinder Sans 14pt w400 `rgba(255,255,255,0.9)`. Photo paginator at top: thin 3pt white segments with 3pt gaps. Bottom-right ⓘ info button (32pt white circle with info glyph in `#424242`). Drag gesture with -15° to 15° rotation based on horizontal velocity."
- "Build the 5 action buttons row below the swipe card: centered horizontal row with 12pt gaps, 32pt below the card. `[Rewind 48pt gold arrow.counterclockwise] [Nope 56pt red xmark] [Super Like 40pt blue star.fill] [Like 56pt green heart.fill] [Boost 48pt purple bolt.fill]`. Each button: white background, 2pt colored stroke ring, 24pt colored SF Symbol centered, shadow `rgba(0,0,0,0.1) 0 2px 8px`. Pressed: scale 0.92 spring with medium haptic, glyph fills the ring briefly."
- "Design the LIKE swipe stamp: green `#00D68F` Tinder Sans 36pt w800 italic, 4pt border in the same green, rotated -15°. Appears top-right of the card as the user drags right, opacity 0 → 1 tied to swipe distance (40% → 100% of threshold). Paired with a medium haptic at commit."
- "Create the Match screen: full-screen linear gradient `#FD267A → #FF6036` horizontal. Top: 'It's a Match!' in Tinder Sans Display 48pt w800 italic white. Subtitle 'You and Sarah liked each other' in Tinder Sans 16pt w400 white 80%. Two 120pt circular profile photos side-by-side with 20pt gap. Bottom: 'Send Message' pill button (white background with gradient text) and 'Keep Playing' outline pill button (2pt white border, white text). Confetti particle burst on entry."
- "Build the primary brand CTA: 52pt tall pill button with horizontal linear gradient background `#FD267A → #FF6036`. Text 'Get Tinder Gold' in Tinder Sans 16pt w700 white with 0.2pt tracking, 14pt vertical / 32pt horizontal padding. Full-pill 32pt corner radius. Shadow `rgba(253,38,122,0.3) 0 4px 16px` (branded pink glow, not black). Pressed: scale 0.97, gradient darkens 10%, medium haptic."
- "Design the bottom tab bar: 64pt + safe area, `#FFFFFF` background with 0.5pt top border `#E5E5E5`. 4 icon-only tabs (no labels): Flame (swipe), Star (Top Picks), Chat bubble, Profile. Icons 28pt. Active icon filled with horizontal pink→orange gradient `#FD267A → #FF6036`; inactive icon outline in `#9E9E9E`."

### Iteration Guide
1. The swipe card is the product — 3:4 aspect, 16pt margins, 16pt radius, black shadow for lift
2. Brand gradient (`#FD267A → #FF6036`) appears only on Match screens and premium CTAs — reserve it
3. Five action buttons = five colors = five verbs: Rewind gold, Nope red, Super Like blue, Like green, Boost purple
4. LIKE / NOPE / SUPER LIKE stamps are rotated 15° italic display type with colored borders — always
5. "Name, 27" formula: name 28pt w700 + age 24pt w400 with a comma between
6. Tab bar is icon-only — no labels, 28pt icons, active tint is the brand gradient
7. Pure black is not on-brand — use `#424242` for body text in light mode
8. Haptics are the soul of the gesture: medium on swipe commit, success on match, strong on Boost
9. Match screen is a full-screen celebration — gradient fills everything, confetti particles burst on entry
