# Design System Inspiration of Quora (iOS)

## 1. Visual Theme & Atmosphere

Quora's iOS app is a bright, paper-white reading environment built around a single tension: a question deserves the gravity of a printed headline, but an answer should feel like a person talking to you. The canvas is pure white (`#FFFFFF`), surfaces are a faint warm gray (`#F7F7F8`), and the only saturated color in the entire interface is Quora Red (`#B92B27`) — held in reserve for the wordmark, the "Answer" call to action, and a thin set of editorial accents. Everything else is text, hairlines, and breathing room. The screen reads like an open book of questions, each one waiting for a considered response.

The defining typographic decision is the serif/sans split. Every question is set in **Georgia**, a transitional serif with sturdy bracketed slabs — it gives the question authority, the feeling of a thing worth answering. The moment you drop into the answer body, the type switches to **Inter**, a neutral humanist sans, optimized for long-form screen reading. This is not decoration: it is the information architecture made visible. A user can scan a feed and instantly tell "this is the prompt" from "this is the discourse" without reading a word. The credential byline beneath each answer — "Former Software Engineer at Google, 2014–2021" — is the third type voice: small, gray, sans, and load-bearing, because on Quora authority is the currency.

Chrome is deliberately quiet. A top segmented tab bar (Home / Following / Answer / Spaces / Notifications-style segments) sits under a slim search field; a bottom navigation bar mirrors the primary destinations for thumb reach. The feed is a vertical stack of question cards separated by a 8pt warm-gray gutter rather than heavy borders. The upvote/downvote control is a rounded pill, not loose icons — a compact unit that travels with each answer. There are no gradients, no glassmorphism, no shadows deeper than a 1px card lift. The app trusts the writing to carry the experience and keeps the container out of the way.

**Key Characteristics:**
- Paper-white canvas (`#FFFFFF`) with warm-gray surfaces (`#F7F7F8`) — a reading app, not a feed app
- Quora Red (`#B92B27`) as the sole brand accent — wordmark, "Answer" CTA, editorial highlights only
- **Serif-question / sans-answer split**: Georgia for every question, Inter for every answer
- Credential byline as a first-class type role — authority is the product
- Upvote/downvote pill — a single compact rounded unit, upvote turns blue (`#2E69FF`) when active
- Top segmented tab + bottom nav (Home / Following / Answer / Spaces / Notifications)
- Spaces row — horizontally scrollable community tiles surfaced in the feed
- "Answer" prompt card — a recurring invitation that nudges contribution
- Hairline dividers (`#E0E0E0`), 1px card lift, zero heavy shadow

## 2. Color Palette & Roles

### Primary
- **Quora Red** (`#B92B27`): Wordmark, "Answer" primary CTA, active editorial accents, link emphasis in branded contexts.
- **Quora Red Pressed** (`#9E1F1B`): Active/pressed state for the red CTA and red interactive elements.
- **Upvote Blue** (`#2E69FF`): Active upvote state, "Upvoted" pill fill text, follow-confirmed accents.

### Canvas & Surface (Light)
- **Canvas White** (`#FFFFFF`): Primary background — question cards, answer body, navigation bars.
- **Surface Gray** (`#F7F7F8`): Feed gutter, grouped sections, search field background, inactive segment fill.
- **Divider** (`#E0E0E0`): Hairline dividers between rows, card outlines, segmented control separators.
- **Text Primary** (`#282829`): Questions (Georgia), answer body (Inter), primary labels.
- **Text Secondary** (`#636466`): Credential byline, metadata, timestamps, upvote counts, secondary labels.
- **Text Tertiary** (`#9A9A9C`): Placeholder text, disabled labels, very low-emphasis captions.

### Canvas & Surface (Dark — Limited Use)
Quora's iOS app is primarily a light reading surface; a dark variant exists for system-dark users.
- **Dark Canvas** (`#1A1A1B`)
- **Dark Surface** (`#262627`)
- **Dark Divider** (`#383839`)
- **Dark Text Primary** (`#E8E8E8`)
- **Dark Text Secondary** (`#A0A0A2`)

### Semantic
- **Upvote Active** (`#2E69FF`): Filled upvote arrow + count.
- **Downvote Active** (`#636466`): Filled downvote arrow (neutral gray, intentionally not red — downvote is quiet).
- **Link Blue** (`#2E69FF`): Inline links inside answer bodies.
- **Success Green** (`#1FA463`): "Answer published" confirmation toast.
- **Error Red** (`#D93025`): Form validation, failed-to-post states (distinct from brand red).

### Quora Red Tints
- **Red Wash** (`#FBEAEA`): Background of a red-bordered notice / "Most viewed writer" ribbon.
- **Red Border** (`#E8B3B1`): 1px outline on red-themed promotional cards.

## 3. Typography Rules

### Font Family
- **Questions / Headlines**: `Georgia` (transitional serif, ships on iOS) — the question voice
- **Answers / UI**: `Inter` (humanist sans) — answer body, all chrome, buttons, labels
- **Fallback Serif**: `Georgia, 'Times New Roman', 'Iowan Old Style', serif`
- **Fallback Sans**: `Inter, -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`
- **Weights**: Georgia 400 / 700; Inter 400 / 600 / 700

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Question (Detail) | Georgia | 26pt | 700 | 1.3 | -0.2pt | Question page hero — the headline |
| Question (Feed Card) | Georgia | 19pt | 700 | 1.35 | -0.1pt | Question title on a feed card |
| Question (Related) | Georgia | 16pt | 400 | 1.4 | 0pt | "Related questions" list rows |
| Answer Body | Inter | 16pt | 400 | 1.6 | 0pt | The core reading size — long-form answer text |
| Answer Lead | Inter | 17pt | 400 | 1.6 | 0pt | First paragraph emphasis on answer page |
| Credential Byline | Inter | 13pt | 600 | 1.35 | 0pt | "Former SWE at Google · 2.1M answer views" |
| Author Name | Inter | 15pt | 700 | 1.3 | 0pt | Answer author display name |
| Section Header | Inter | 18pt | 700 | 1.3 | -0.1pt | "Spaces to follow", "Top questions" |
| Metadata | Inter | 13pt | 400 | 1.35 | 0pt | "Answered 3y ago", view counts |
| Upvote Count | Inter | 14pt | 600 | 1.0 | 0pt | Number inside the vote pill |
| Button (Primary) | Inter | 15pt | 700 | 1.0 | 0.1pt | Red "Answer" button label |
| Button (Secondary) | Inter | 14pt | 600 | 1.0 | 0pt | "Follow", "Share" outline buttons |
| Tab Label (Bottom) | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom navigation labels |
| Segment Label (Top) | Inter | 14pt | 700 | 1.0 | 0pt | Top segmented control tabs |
| Caption | Inter | 12pt | 400 | 1.35 | 0pt | Footnotes, "Sponsored", legal |

### Principles
- **Serif is the question, sans is everything else**: never set a question in Inter or an answer in Georgia — the split is structural
- **Answer body is sacred at 16pt / line-height 1.6**: the app is read, not skimmed; generous leading is non-negotiable
- **Credential byline is semibold gray** — it must be quietly authoritative, never shouting
- **Weight discipline**: Georgia only 400/700, Inter only 400/600/700 — no light, no black
- **Negative tracking only on large serif** (-0.2pt max on the detail question); body sits at 0pt
- **Dynamic Type respected on question + answer text**; vote counts and tab labels are layout-fixed

## 4. Component Stylings

### Buttons

**Primary "Answer" CTA**
- Background: `#B92B27`
- Text: `#FFFFFF`, Inter 15pt weight 700, letter-spacing 0.1pt
- Padding: 11pt vertical, 24pt horizontal
- Corner radius: 6pt
- Icon (optional): pencil glyph 16pt leading, 6pt gap
- Pressed: background `#9E1F1B`, scale 0.98
- Used for: "Answer" on a question card, "Add answer" on detail page

**Secondary Outline ("Follow" / "Share")**
- Background: `#FFFFFF`
- Text: `#282829`, Inter 14pt weight 600
- Border: 1pt solid `#E0E0E0`
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 6pt
- Pressed: background `#F7F7F8`
- Followed state: text `#2E69FF`, border `#2E69FF`, label "Following"

**Upvote / Downvote Pill (Signature)**
- Container: single rounded pill, height 32pt, border 1pt `#E0E0E0`, background `#FFFFFF`, corner radius 16pt
- Left segment: up-arrow glyph 16pt + count (Inter 14pt w600 `#636466`)
- Vertical hairline divider `#E0E0E0` between up and down
- Right segment: down-arrow glyph 16pt
- Active upvoted: left segment fill `#EAF0FF`, arrow + count `#2E69FF`
- Active downvoted: right arrow `#636466` filled (neutral — never red)
- Pressed: subtle scale 0.96 + soft haptic

**Text Link ("More", "Continue Reading")**
- Inter 14pt weight 600, color `#2E69FF`
- No underline; 44pt hit area; pressed opacity 0.6

**Icon Action (Comment, Share, Bookmark, More)**
- Glyph 18pt, hit area 44pt, color `#636466`
- Active bookmark: `#B92B27`
- Spacing: 24pt between actions in the answer footer row

### Cards & Containers

**Question Feed Card**
- Background: `#FFFFFF`, full bleed, separated by 8pt `#F7F7F8` gutter (no border)
- Inner padding: 16pt
- Structure: author row (24pt avatar + name + credential) → **Georgia question 19pt w700** → answer preview (Inter 16pt w400, 3-line clamp, `#282829`) → "(more)" link → footer (vote pill + comment + share)
- Tap question: pushes question detail with crossfade
- Pressed card: background `#FAFAFA`

**Answer Card (Detail Page)**
- Background: `#FFFFFF`
- Author block: 40pt circular avatar + name (15pt w700) + **credential byline (13pt w600 `#636466`)** + "Answered 3y ago"
- Body: Inter 16pt w400, line-height 1.6, paragraph spacing 14pt
- Footer: upvote/downvote pill (leading) + comment count + share + bookmark + ⋯
- Divider `#E0E0E0` between stacked answers, 24pt vertical padding around it

**Spaces Row**
- Horizontally scrollable row of Space tiles surfaced in the feed
- Tile: 140pt wide, `#FFFFFF`, 1pt `#E0E0E0` border, corner radius 10pt
- Banner strip 64pt tall (Space color), 40pt Space icon overlapping, name (Inter 15pt w700), member count (13pt `#636466`), "Follow" outline button
- Section header: "Spaces for you" (Inter 18pt w700) + "See all" link

**"Answer" Prompt Card**
- Background: `#FFFFFF`, 1pt `#E0E0E0` border, corner radius 10pt, padding 16pt
- Prompt question in **Georgia 17pt w700**
- Subtext "Share your knowledge" (Inter 13pt `#636466`)
- Full-width red "Answer" button at the bottom

**Search Result Row**
- Height 60pt, leading 20pt magnifier or topic icon, Georgia 16pt w400 question text, trailing chevron `#9A9A9C`
- Divider `#E0E0E0`, pressed `#F7F7F8`

### Navigation

**Top Segmented Tab Bar**
- Height: 44pt, background `#FFFFFF`, bottom hairline `#E0E0E0`
- Segments: scrollable text tabs (Inter 14pt w700)
- Active: text `#282829` with a 2pt `#B92B27` underline indicator beneath the label
- Inactive: text `#636466`
- Search field directly above: `#F7F7F8` fill, 36pt tall, corner radius 8pt, leading magnifier 16pt `#636466`, placeholder "Search Quora"

**Bottom Navigation Bar**
- Height: 52pt + safe area, background `#FFFFFF`, top hairline `#E0E0E0`
- Tabs: Home, Following, **Answer (center, red glyph)**, Spaces, Notifications
- Icon 24pt; active `#282829`, inactive `#636466`
- Center "Answer" tab: glyph rendered in `#B92B27` always (the contribution anchor)
- Labels: Inter 10pt w600, always shown
- Notifications tab: red dot badge `#B92B27` when unread

**Question Detail Top Bar**
- Height: 44pt + safe area, background `#FFFFFF`, bottom hairline on scroll
- Leading: back chevron 22pt `#282829`
- Center: "Question" label (Inter 15pt w700) — appears on scroll past hero
- Trailing: share 20pt + ⋯ 20pt, both `#636466`

### Input Fields

**Search Field**
- Background: `#F7F7F8`
- Height: 36pt, corner radius 8pt
- Leading SF-style magnifier 16pt `#636466`
- Placeholder: "Search Quora", Inter 15pt w400 `#9A9A9C`
- Focus: 1pt `#B92B27` border, background `#FFFFFF`, cursor `#B92B27`

**Answer Composer**
- Full-screen sheet, `#FFFFFF`
- The question shown at top in **Georgia 19pt w700** (you always see what you're answering)
- Body editor: Inter 16pt w400, line-height 1.6, placeholder "Write your answer…" `#9A9A9C`
- Toolbar: bold / italic / link / image / quote glyphs, `#636466`, 22pt
- Top-right: red "Post" button (disabled `#E8B3B1` until non-empty)

### Distinctive Components

**The Serif/Sans Split**
- Every question — feed card, detail hero, related list, composer header, "Answer" prompt — is **Georgia**
- Every answer body, byline, label, button, metadata is **Inter**
- This is the single most identifying trait of the app: a Quora screen is recognizable from typographic contrast alone

**Upvote/Downvote Pill**
- One bordered capsule, upvote count visible, downvote arrow only (no count — Quora hides downvote totals)
- Upvote active → blue (`#2E69FF`) wash + blue arrow/count; downvote active → gray fill, never red
- Animation: arrow fills + count increments with a 200ms ease, soft haptic

**Credential Byline**
- Sits directly under the author name on every answer
- Inter 13pt w600 `#636466`, may include a tiny check or "Most Viewed Writer" red ribbon
- It is the trust signal — given typographic priority over timestamp

**Spaces Carousel**
- Community tiles with a colored banner, overlapping round icon, follow CTA
- Surfaced inline between feed cards — the only place color (per-Space) enters the feed

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Card inner padding: 16pt
- Feed gutter between cards: 8pt (`#F7F7F8`, replaces borders)
- Answer paragraph spacing: 14pt
- Action row icon spacing: 24pt

### Grid & Container
- Content width: full device width, 16pt horizontal insets inside cards
- Feed: single column of full-bleed cards
- Spaces row: horizontal scroll, 140pt tiles, 12pt gap, 16pt leading inset
- No multi-column grids — Quora is a single-column reading column

### Whitespace Philosophy
- **Generous line-height in answers**: 1.6 on 16pt body — readability over density
- **Quiet gutters, not loud borders**: cards float on an 8pt gray gutter, hairlines only where structurally needed
- **The question gets air**: 16–24pt above and below the Georgia question to let it act as a headline
- **Restraint as identity**: whitespace + type contrast carry the brand; color is rationed

### Border Radius Scale
- Square (0pt): full-bleed feed cards (separated by gutter, not radius)
- Soft (6pt): buttons, search field-adjacent inputs, "Answer" CTA
- Standard (8pt): search field, inline notices
- Comfortable (10pt): Space tiles, prompt cards, bottom sheets
- Pill (16pt): upvote/downvote capsule
- Round (50%): avatars, Space icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed cards, list rows, canvas — separated by gutter/hairline |
| Card Lift (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` | Space tiles, prompt cards |
| Sticky Bar (Level 2) | `rgba(0,0,0,0.05) 0 1px 0` (hairline shadow) | Top segmented bar + bottom nav on scroll |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -8px 32px` | Answer composer, share sheet, context menu |
| Toast (Level 4) | `rgba(0,0,0,0.16) 0 6px 20px` | "Answer published" confirmation |

**Shadow Philosophy**: Quora is a paper metaphor — shadows are nearly absent. The deepest shadow in the steady-state UI is a 3px, 6%-opacity card lift on Space tiles. Real elevation only appears on transient surfaces (composer, sheets, toasts). The white canvas means a heavy shadow would feel like dirt; the app keeps everything flat and lets type hierarchy create depth instead.

### Motion
- **Upvote tap**: arrow fills + count rolls +1 over 200ms ease-out, paired with `.impactOccurred(.light)`
- **Pill press**: scale 1.0 → 0.96 → 1.0, 150ms
- **Card tap → detail**: crossfade + 8pt upward slide, 280ms
- **Composer present**: sheet slides up 320ms spring (damping 0.85)
- **Follow toggle**: label crossfades "Follow" ↔ "Following", border color tween 180ms
- **Pull-to-refresh**: minimal system spinner, no custom flourish

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) canvas with `#F7F7F8` surfaces — it is a reading app
- Set **every question in Georgia** and **every answer in Inter** — the split is the brand
- Reserve Quora Red (`#B92B27`) for the wordmark, the "Answer" CTA, and editorial accents only
- Give the answer body 16pt with line-height 1.6 — leading is non-negotiable
- Render the credential byline in semibold gray — authority is the product
- Keep the upvote/downvote as one bordered pill; upvote-active is blue, never red
- Separate feed cards with an 8pt gray gutter instead of borders or shadows
- Keep the center bottom-nav "Answer" glyph red at all times

### Don't
- Don't set questions in a sans font or answers in a serif — never break the split
- Don't add accent colors beyond red + upvote blue — the restraint is the identity
- Don't make downvote red — it is a quiet neutral gray; Quora doesn't punish visually
- Don't tighten answer line-height below 1.5 — it kills long-form readability
- Don't use heavy shadows on the white canvas — max is a 6% 3px card lift
- Don't show downvote counts — Quora deliberately hides them
- Don't bury the credential byline below the timestamp — trust outranks recency
- Don't use display-size headlines — the Georgia question at 26pt is the largest type

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Feed question 18pt, 3-line answer clamp stays |
| iPhone 13/14/15 | 390pt | Standard — feed question 19pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears top bar |
| iPhone 15/16 Pro Max | 430pt | Detail question scales to 28pt, wider reading column |
| iPad | 768pt+ | Centered 680pt max reading column, Spaces in 2-up grid |

### Dynamic Type
- Question (Georgia) and answer body (Inter): full scale
- Detail question caps at 30pt; feed question caps at 22pt
- Upvote count, tab labels, segment labels: layout-fixed (never scale)
- Credential byline: scales one step then clamps

### Orientation
- Feed / Question detail / Spaces: **portrait-locked**
- Answer composer: portrait-locked (writing surface)
- Inline images in answers: tap to open full-screen viewer (rotates freely)

### Touch Targets
- "Answer" CTA: 44pt min height
- Upvote/downvote pill: 32pt tall, each segment ≥44pt effective hit
- Bottom nav items: 24pt icon, 49pt hit
- Inline links in answer body: 44pt vertical hit padding

### Safe Area Handling
- Top: segmented bar + search respect safe area / Dynamic Island
- Bottom: navigation bar respects home indicator
- Sides: 16pt content insets; reading column never touches the edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F7F7F8`
- Divider: `#E0E0E0`
- Text primary: `#282829`
- Text secondary: `#636466`
- Text tertiary: `#9A9A9C`
- Quora Red (CTA / wordmark): `#B92B27`
- Quora Red pressed: `#9E1F1B`
- Upvote blue (active): `#2E69FF`
- Success green: `#1FA463`

### Example Component Prompts
- "Create a SwiftUI Quora question feed card: white background, 16pt padding, separated by an 8pt #F7F7F8 gutter (no border). Top: 24pt circular avatar + author name 'Dana Rourke' Inter 15pt w700 #282829 + credential 'Former Data Scientist · 1.4M views' Inter 13pt w600 #636466. Then the question in **Georgia 19pt weight 700 #282829** 'Why do experienced engineers prefer boring technology?'. Then a 3-line-clamped answer preview in Inter 16pt w400 #282829 ending with a blue '(more)' link. Footer: upvote/downvote pill, comment, share."
- "Build the Quora upvote/downvote pill: one capsule 32pt tall, 1pt #E0E0E0 border, 16pt corner radius, white background. Left segment: up-arrow 16pt + count '1.2K' Inter 14pt w600 #636466. 1pt vertical #E0E0E0 divider. Right segment: down-arrow 16pt #636466. Upvoted state: left segment background #EAF0FF, arrow and count #2E69FF. Press: scale 0.96 with light haptic. Downvote never turns red."
- "Design the Quora answer detail block: white background. 40pt circular avatar, author name Inter 15pt w700, credential byline Inter 13pt w600 #636466 'Former SWE at Google · 2.1M answer views', 'Answered 3y ago' Inter 13pt w400 #636466. Body: Inter 16pt weight 400, line-height 1.6, 14pt paragraph spacing, #282829. Footer row: upvote/downvote pill, comment count, share, bookmark, ellipsis — icons 18pt #636466, 24pt spacing. Divider #E0E0E0 below."
- "Create the Quora Spaces carousel: section header 'Spaces for you' Inter 18pt w700 #282829 + 'See all' link #2E69FF. Horizontal scroll of 140pt tiles, white, 1pt #E0E0E0 border, 10pt radius: a 64pt colored banner, a 40pt round Space icon overlapping it, Space name Inter 15pt w700, member count Inter 13pt #636466, and a small outline 'Follow' button."
- "Build the Quora bottom navigation: 52pt + safe area, white, top hairline #E0E0E0. Five tabs Home / Following / Answer / Spaces / Notifications. Icons 24pt; active #282829, inactive #636466. The center 'Answer' tab glyph is always #B92B27. Labels Inter 10pt w600. Notifications shows a #B92B27 dot badge when unread."

### Iteration Guide
1. Canvas is pure `#FFFFFF`, surfaces `#F7F7F8` — this is a reading app, keep it bright
2. **Questions are Georgia, answers are Inter** — never swap; the split is the entire identity
3. Quora Red (`#B92B27`) only for wordmark, "Answer" CTA, and editorial accents — nothing else
4. Answer body is 16pt with line-height 1.6 — do not compress leading
5. Credential byline is semibold gray and ranks above the timestamp — authority is the product
6. Upvote/downvote is one bordered pill; upvote-active = blue, downvote = neutral gray (never red)
7. Feed cards separate with an 8pt gray gutter, not borders or shadows
8. Shadows are almost absent — paper metaphor; max 6% 3px card lift, real elevation only on sheets
