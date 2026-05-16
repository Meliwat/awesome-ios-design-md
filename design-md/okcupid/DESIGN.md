# Design System Inspiration of OkCupid (iOS)

## 1. Visual Theme & Atmosphere

OkCupid's iOS app is a bright, friendly, question-driven matchmaker. The canvas is clean white (`#FFFFFF`) with a soft neutral surface (`#F2F2F2`) for grouped sections and cards. The brand voice is playful and editorial — illustrated empty states, rounded everything, and warm copy — but the underlying structure is data: every profile is annotated with a **match percentage**, and matching is powered by the answers people give to thousands of personality questions. The product thesis ("substance over swipe") shows up visually as profiles that lead with text and questions, not just photos.

The accent system is anchored by OkCupid Magenta (`#E2024F`) — the exact hot pink-red of the brand wordmark. It owns the primary action (the "Like" heart, the primary CTA pill, the active tab) and the match-percentage badge. A secondary brand indigo (`#0500FF`) is used sparingly for the "Pass" affordance, secondary links, and accents in illustrations. Everything else is ink, gray, or the soft neutral surface. The magenta is energetic and unmistakable; the indigo keeps the like/pass duality balanced.

Typography is Larsseit (OkCupid's brand typeface), rendered here with Inter as the closest free substitute, at weights 400 / 600 / 700. The hierarchy is rounded and confident: 12–30pt, with the match percentage getting its own oversized treatment (the circular badge number can run 18–22pt bold in magenta). Screen titles are 28–30pt bold; question prompts — the heart of the app — get a generous 20pt semibold so they read like a friendly interview. The most expressive type moment is the DoubleTake profile card, where the name + age + match-% headline stacks over the photo with rounded, high-contrast type.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) with a soft neutral surface (`#F2F2F2`)
- OkCupid Magenta (`#E2024F`) as the primary accent — Like, primary CTA, active tab, match-%
- Brand indigo (`#0500FF`) as a secondary accent — Pass, links, illustration detail
- Match-percentage circular badge — the signature data ornament on every profile
- DoubleTake profile stack — full card UI, swipe-like browsing with substance
- Match-question cards — answer prompts that drive compatibility, the core loop
- Playful illustrated empty states — friendly, on-brand line art
- Like / Pass round buttons — magenta heart, indigo pass, anchored to the card
- Larsseit typeface (Inter fallback), rounded grotesque, weights 400/600/700

## 2. Color Palette & Roles

### Primary
- **OkCupid Magenta** (`#E2024F`): Primary CTA, "Like" heart, active bottom-tab item, match-% badge ring + number, selected toggles.
- **Magenta Pressed** (`#C00244`): Active/pressed state for magenta CTAs.
- **Magenta Tint** (`#FCE3EB`): Like-flash background, selected chip fill, focused-field ring wash.

### Secondary
- **Brand Indigo** (`#0500FF`): "Pass" affordance, secondary links, illustration accents, info highlights.
- **Indigo Tint** (`#E5E4FF`): Pass-flash background, indigo chip fill.

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary canvas — card backgrounds, sheets, nav bar.
- **Surface Neutral** (`#F2F2F2`): Grouped sections, the screen background behind cards, question shelves.
- **Surface Raised** (`#FFFFFF` + shadow): Cards lift via soft shadow on the neutral surface.
- **Divider** (`#E5E5E5`): Hairline between list rows and section groups.
- **Text Primary** (`#14171A`): Names, screen titles, primary UI text (near-black, slightly cool).
- **Text Secondary** (`#6B6B6B`): Metadata, subtitles, placeholders, question helper text.
- **Text Tertiary** (`#9B9B9B`): Disabled labels, very low-emphasis captions.

### Match Percentage
- **Match High** (`#E2024F`): 90–100% — full magenta ring + number.
- **Match Mid** (`#F0578A`): 70–89% — lighter magenta ring.
- **Match Low** (`#9B9B9B`): below 70% — neutral gray ring (de-emphasized).

### Semantic
- **Success Green** (`#1CAA6A`): "It's a Match!" confirmation, verified badge.
- **Warning Amber** (`#F5A623`): Incomplete-profile nudge, "Answer more questions" prompt.
- **Error Red** (`#D0021B`): Failed action, blocked-user, sign-in error.

### Dark Mode (Limited)
OkCupid's iOS app is primarily light. A dark variant exists for system-dark users.
- **Dark Canvas** (`#15151A`)
- **Dark Surface** (`#1F1F26`)
- **Dark Text** (`#FFFFFF`)
- **Magenta stays `#E2024F`** — it reads well on dark; ring de-emphasis uses `#5C5C66`.

## 3. Typography Rules

### Font Family
- **Primary**: `Larsseit` (OkCupid brand typeface, by Type Dynamic)
- **Substitute / Fallback**: `Inter` — closest free rounded-grotesque match
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Tone**: Friendly rounded grotesque — geometric but warm; never a hard industrial sans

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Larsseit | 30pt | 700 | 1.15 | -0.4pt | "DoubleTake", "Matches", nav large title |
| Profile Name (Hero) | Larsseit | 26pt | 700 | 1.15 | -0.3pt | Name + age on the profile card hero |
| Section Header | Larsseit | 22pt | 700 | 1.2 | -0.2pt | "Your matches", "Questions" |
| Match % (Badge) | Larsseit | 20pt | 700 | 1.0 | 0pt | The circular percentage number |
| Question Prompt | Larsseit | 20pt | 600 | 1.3 | -0.1pt | A match-question's text — the core loop |
| Card / List Title | Larsseit | 17pt | 600 | 1.3 | -0.1pt | Match-card name, list row title |
| Body | Larsseit | 16pt | 400 | 1.5 | 0pt | Profile bio, question explanations |
| Answer Option | Larsseit | 16pt | 600 | 1.3 | 0pt | Selectable answer in a question card |
| Metadata | Larsseit | 14pt | 400 | 1.35 | 0pt | "Last online today", subtitle |
| Caption / Count | Larsseit | 13pt | 400 | 1.3 | 0pt | "12 new likes", small counts |
| Label (UPPER) | Larsseit | 11pt | 700 | 1.2 | 0.6pt | "MATCH QUESTIONS" section labels |
| Button (Primary) | Larsseit | 17pt | 700 | 1.0 | 0.2pt | Magenta primary pill |
| Tab Label | Larsseit | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |

### Principles
- **Weights at 400 / 600 / 700**: Regular for body, semibold for prompts/options/titles, bold for headlines and the match number — no thin
- **Rounded warmth, not industrial**: Larsseit's soft terminals carry the friendly brand voice; pick Inter (not a hard grotesque) as the substitute
- **The match % is typographically loud**: it always gets bold, large, and magenta when high
- **Negative tracking on large titles only**: -0.4pt at 30pt; body and questions sit at 0 / -0.1pt
- **Dynamic Type respected**: titles, questions, body scale; the badge number and tab labels are pinned

## 4. Component Stylings

### Buttons

**Primary Pill (Send Like / Continue / It's a Match)**
- Background: `#E2024F`
- Text: `#FFFFFF`
- Padding: 15pt vertical, 32pt horizontal
- Corner radius: 500pt (full pill)
- Font: Larsseit, 17pt, weight 700, +0.2pt tracking
- Pressed: background `#C00244`, scale 0.97, soft haptic
- Disabled: `#E2024F` at 35% opacity

**Like Button (The Magenta Heart)**
- Shape: Circle, 64pt diameter (on the profile card action bar)
- Background: `#FFFFFF` with a 1pt `#E5E5E5` ring and a soft shadow
- Icon: heart glyph 28pt, `#E2024F`
- Pressed: scale 0.90, a `#FCE3EB` flash fills the circle, success haptic
- Liked state: heart fills solid `#E2024F` with a 1.0 → 1.2 → 1.0 bounce

**Pass Button (The Indigo X)**
- Shape: Circle, 56pt diameter
- Background: `#FFFFFF` with a 1pt `#E5E5E5` ring and a soft shadow
- Icon: X glyph 24pt, `#0500FF`
- Pressed: scale 0.90, an `#E5E4FF` flash, light haptic; card slides away

**Secondary / Outline Pill**
- Background: transparent
- Text: `#14171A`
- Border: 1.5pt solid `#14171A`
- Padding: 14pt vertical, 28pt horizontal, corner radius 500pt
- Font: Larsseit, 16pt, weight 600
- Pressed: background `#F2F2F2`

**Text Button ("Skip", "See more")**
- Font: Larsseit, 15pt, weight 600
- Color: `#0500FF`
- No background, no underline, 44pt hit area

### Cards & Containers

**DoubleTake Profile Card (The Signature)**
- Full-width card, ~96% of screen, rounded 24pt corners, soft shadow
- Top: hero photo (4:5), name + age (26pt w700) and a match-% badge overlaid bottom-left on a subtle scrim
- Scrollable below: bio, prompt answers, photo gallery, "the two of us agree" question highlights
- Bottom action bar pinned: Pass (56pt indigo), Like (64pt magenta, centered/larger), Star/Boost
- Card swipes/slides on Pass; Like triggers the magenta flash + bounce

**Match-% Circular Badge**
- Diameter: 48pt (on cards) / 36pt (in list rows)
- Ring: 3pt stroke; color by tier — `#E2024F` (90+), `#F0578A` (70–89), `#9B9B9B` (<70)
- Center: the number (e.g., "94%") in Larsseit 20pt w700, color matching the ring tier
- Background fill: `#FFFFFF` (96% opacity over photos) so it always reads
- Subtle count-up animation on first appearance (0 → value over 600ms)

**Match-Question Card**
- White card, rounded 20pt, soft shadow, 20pt internal padding
- Prompt: 20pt w600 `#14171A` (the question)
- Answer options: stacked full-width pills, 16pt w600, 1.5pt `#E5E5E5` border; selected = `#E2024F` fill + white text
- Footer: an "importance" segmented control + a magenta "Answer" primary pill
- Optional explanation field below for free-text context

**Match List Row**
- Height: 76pt
- Layout: 56pt circular avatar (with a small match-% badge clipped bottom-right) → name (17pt w600) + last activity (14pt w400 `#6B6B6B`) → trailing chevron or unread magenta dot
- Background: `#FFFFFF`; Pressed: `#F2F2F2`

**Illustrated Empty State**
- Centered friendly line illustration (~160pt) using magenta + indigo strokes
- Headline 22pt w700, supportive body 16pt w400 `#6B6B6B`
- A magenta primary pill CTA ("Answer questions" / "Browse matches")

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.96)` with `.regularMaterial` blur, 0.5pt top divider `#E5E5E5`
- Tabs: Discover, Likes, Matches, Messages, Profile
- Icon size: 26pt
- Active color: `#E2024F` (icon + label)
- Inactive: `#6B6B6B`
- Labels: Larsseit 10pt w600, always shown
- Badge: magenta `#E2024F` pill with white count on Likes / Messages

**Top Bar**
- Height: 44pt + safe area
- Large title (30pt w700 `#14171A`) collapsing to inline 17pt on scroll
- Trailing: filter icon (22pt) + settings (22pt), `#14171A`
- Translucent white blur on scroll

### Input Fields

**Text Field (Bio / Free-text answer)**
- Background: `#FFFFFF`
- Border: 1.5pt solid `#E5E5E5`, corner radius 14pt
- Min height: 52pt, 16pt padding
- Placeholder: 16pt w400 `#9B9B9B`
- Focused: border `#E2024F` 2pt, subtle `#FCE3EB` ring
- Error: border `#D0021B`, helper text `#D0021B` 13pt

**Message Composer**
- Background: `#F2F2F2`, corner radius 24pt, min height 48pt
- Placeholder: "Say something", 16pt w400 `#9B9B9B`
- Trailing send: `#9B9B9B` when empty → filled `#E2024F` circle when text present

**Importance Segmented Control (on questions)**
- Pill segments: "A little" / "Somewhat" / "Very"
- Unselected: `#F2F2F2` fill, `#6B6B6B` text; Selected: `#E2024F` fill, white text
- Corner radius 500pt, 13pt w600 labels

### Distinctive Components

**Match Percentage System**
- Every browsable profile and match carries a circular % badge whose color tier signals fit at a glance (magenta = great, light magenta = good, gray = low)
- The number animates with a quick count-up the first time a profile is seen — the data is the hook

**DoubleTake Stack**
- A full-card browsing experience (not a thin Tinder swipe): one rich profile at a time with bio, questions, and photos; Pass slides the card out, Like flashes magenta and advances
- The card is scrollable internally so substance (answers) is reachable before deciding

**Match-Question Loop**
- The core engagement loop: answer a question → pick how important it is → see compatibility update
- Question cards are visually distinct (rounded, padded, option-pill stacks) and reused across onboarding, profiles, and a dedicated Questions tab

**Playful Illustration Language**
- Empty states, success moments ("It's a Match!"), and onboarding use friendly two-color (magenta + indigo) line illustrations — warmth is a brand pillar, never a sterile blank state

**It's a Match Celebration**
- Full-screen takeover: both avatars, a large magenta "It's a Match!" headline, confetti-light motion, and a magenta "Send a message" primary pill

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal
- Card internal padding: 20pt; section spacing: 24pt between question shelves
- Action-bar button spacing: 24pt between Pass / Like / Star

### Grid & Container
- Content width: full device width, 16pt horizontal margins
- DoubleTake card: ~96% width, centered, 24pt radius
- Question option pills: full card width, 12pt vertical gap
- Matches grid (optional): 2-column, 12pt gap, 16pt card radius

### Whitespace Philosophy
- **Generous and friendly**: 20pt card padding and roomy line-height (1.5 body) make profiles feel like reading, not skimming — substance over swipe
- **Cards float on neutral**: the `#F2F2F2` background lets white cards lift with a soft shadow, giving the playful, tactile feel
- **The match % gets air**: the badge is never crowded — it's a focal ornament

### Border Radius Scale
- Standard (14pt): Text fields, small chips
- Comfortable (16pt): List/section cards, grid tiles
- Rounded (20pt): Question cards
- Card (24pt): DoubleTake profile card
- Sheet (20pt): Bottom sheets / modals (top corners)
- Full Pill (500pt): Primary CTAs, answer-option pills, segmented control, chips
- Circle (50%): Avatars, Like/Pass buttons, match-% badge

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Section groups on neutral, list rows |
| Card (Level 1) | `rgba(20,23,26,0.06) 0 2px 8px` | Question cards, match cards, list cards |
| Profile Card (Level 2) | `rgba(20,23,26,0.12) 0 8px 24px` | The DoubleTake profile card |
| Action Button (Level 3) | `rgba(20,23,26,0.16) 0 4px 14px` | Floating Like / Pass circular buttons |
| Sheet (Level 4) | `rgba(20,23,26,0.18) 0 -12px 32px` | Bottom sheets, "It's a Match" takeover |
| Scrim | `rgba(20,23,26,0.40)` | Modal dim, behind takeover celebrations |

**Shadow Philosophy**: Shadows are soft, cool-tinted (built from the `#14171A` ink), and low-opacity — they make white cards float gently on the `#F2F2F2` neutral surface, reinforcing the playful, tactile feel. The DoubleTake card and the Like/Pass buttons get the most lift because they are the interactive heart of the app. Everything in plain list/section groups stays flat.

### Motion
- **Like tap**: button scale 0.90 → 1.0 spring, `#FCE3EB` flash, heart fill bounce 1.0 → 1.2 → 1.0, `.notificationOccurred(.success)`
- **Pass tap**: button scale 0.90, `#E5E4FF` flash, card slides off-screen (0.3s ease-out) with slight rotation
- **Match-% count-up**: number animates 0 → value over 600ms ease-out the first time a card appears
- **Card swipe**: 60fps horizontal/vertical card drag with spring snap-back or commit
- **It's a Match**: full-screen scale-in (0.85 → 1.0), light confetti, avatars slide together over 500ms
- **Tab switch**: instant; active glyph fills and recolors to `#E2024F`
- **Pull-to-refresh**: minimal magenta spinner, 0.4s

## 7. Do's and Don'ts

### Do
- Use a bright white canvas (`#FFFFFF`) with the soft neutral surface (`#F2F2F2`) behind cards
- Reserve OkCupid Magenta (`#E2024F`) for the Like, primary CTA, active tab, and match-% badge
- Use indigo (`#0500FF`) for Pass, secondary links, and illustration detail — keep the like/pass duality balanced
- Show a circular match-% badge on every browsable profile, color-tiered by fit
- Animate the match number with a quick count-up the first time a profile appears
- Make question prompts large and friendly (20pt semibold) — substance is the brand
- Round everything generously — 20–24pt cards, full-pill buttons and answer options
- Use friendly two-color (magenta + indigo) illustrations for empty and success states
- Use Larsseit (Inter fallback) at weights 400/600/700 only

### Don't
- Don't use a thin/industrial substitute font — pick a rounded grotesque (Inter), Larsseit is warm
- Don't add accent colors beyond magenta + indigo to UI chrome — match-tier colors are derived from magenta
- Don't make the match % small or gray when it's high — when it's a great match, it must be loud and magenta
- Don't reduce profiles to thin swipe cards — DoubleTake is rich and internally scrollable
- Don't ship a sterile blank empty state — always use the playful illustration language
- Don't use sharp corners — the brand is rounded; minimum card radius is 16pt
- Don't use thin font weights — the ramp is 400/600/700
- Don't put magenta and indigo in equal weight on the same control — magenta leads, indigo supports
- Don't skip the "It's a Match" celebration — it is a signature emotional beat

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Profile card ~98% width; smaller hero (4:5 maintained) |
| iPhone 13/14/15 | 390pt | Standard ~96% DoubleTake card |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the large title nav |
| iPhone 15/16 Pro Max | 430pt | Larger hero, match-% badge scales to 52pt |
| iPad | 768pt+ | Two-pane: match list + profile detail; questions in a 2-column grid |

### Dynamic Type
- Titles, question prompts, body, list text: full scale
- Match-% badge number: pinned (badge is a fixed ornament)
- Tab labels: fixed 10pt
- Profile hero name: scales up to 30pt before clamping

### Orientation
- Discover / DoubleTake / Matches: **portrait-locked** (the card is tuned for portrait)
- Photo viewer: **rotates** to fit landscape images
- iPad: **all orientations**, two-pane layout persists

### Touch Targets
- Like button: 64pt circle — the primary, largest action
- Pass button: 56pt circle
- Answer-option pills: full-width, 52pt min height
- List rows: 76pt height, full-row tappable
- Tab bar icons: 26pt glyph, 44pt effective hit

### Safe Area Handling
- Top: large title respects safe area + Dynamic Island
- Bottom: tab bar + the floating action bar respect the home indicator
- DoubleTake card: respects safe area; the action bar floats above the tab bar
- "It's a Match" takeover: full-bleed, controls inside safe area

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface (neutral): `#F2F2F2`
- Divider: `#E5E5E5`
- Text primary: `#14171A`
- Text secondary: `#6B6B6B`
- Text tertiary: `#9B9B9B`
- OkCupid Magenta (action): `#E2024F`
- Magenta pressed: `#C00244`
- Magenta tint (flash): `#FCE3EB`
- Brand indigo (Pass/links): `#0500FF`
- Match mid: `#F0578A`
- Success green: `#1CAA6A`
- Error red: `#D0021B`

### Example Component Prompts
- "Create a SwiftUI OkCupid match-% badge: a 48pt circle, white fill at 96% opacity, a 3pt ring whose color is `#E2024F` for 90%+, `#F0578A` for 70–89%, `#9B9B9B` below 70%. Centered number e.g. '94%' in Inter 20pt weight 700 matching the ring tier. On first appearance, count the number up from 0 to the value over 600ms ease-out."
- "Build the Like button: 64pt circular button, white background with a 1pt `#E5E5E5` ring and a soft shadow, a heart glyph 28pt `#E2024F`. Pressed: scale 0.90, a `#FCE3EB` flash fills the circle, success haptic; liked state fills the heart solid `#E2024F` with a 1.0 → 1.2 → 1.0 bounce."
- "Design a match-question card: white card, 20pt corner radius, soft shadow, 20pt padding. Prompt in Inter 20pt weight 600 `#14171A`. Below: full-width answer-option pills (16pt weight 600, 1.5pt `#E5E5E5` border; selected = `#E2024F` fill + white text). Footer: a pill segmented importance control ('A little / Somewhat / Very') and a magenta 'Answer' primary pill."
- "Create the DoubleTake profile card: ~96% width, 24pt corner radius, soft shadow. Hero photo 4:5 with name + age 'Maya, 27' in Inter 26pt weight 700 and a 48pt match-% badge overlaid bottom-left on a subtle scrim. Internally scrollable bio + question highlights. Pinned bottom action bar: Pass (56pt, indigo X) on the left, Like (64pt, magenta heart, larger/centered), Star on the right."
- "Build the bottom tab bar: 5 tabs (Discover, Likes, Matches, Messages, Profile), 26pt icons, active `#E2024F`, inactive `#6B6B6B`, labels Inter 10pt weight 600. `rgba(255,255,255,0.96)` material background, 0.5pt `#E5E5E5` top divider. Magenta count badges on Likes and Messages."

### Iteration Guide
1. Canvas is bright white `#FFFFFF`; cards float on the neutral `#F2F2F2` surface
2. Magenta `#E2024F` leads (Like, primary CTA, active tab, match-%); indigo `#0500FF` supports (Pass, links)
3. Every browsable profile shows a circular match-% badge, color-tiered by fit, with a count-up on first view
4. Question prompts are large and friendly (20pt semibold) — substance over swipe is the brand
5. Round everything generously — 20–24pt cards, full-pill buttons and answer options
6. Use the playful two-color illustration language for empty and success states; never a sterile blank
7. Typography: Larsseit (Inter fallback), rounded grotesque, weights 400/600/700 — no thin
8. The "It's a Match" celebration is a signature beat — full-screen, magenta, joyful
