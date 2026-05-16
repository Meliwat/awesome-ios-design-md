# Design System Inspiration of LINE (iOS)

## 1. Visual Theme & Atmosphere

LINE's iOS app is a messaging surface where the sticker is the unit of expression. Most messengers treat stickers as a feature; LINE treats them as the medium. The single most identifying visual is the **oversized sticker message**: when you send a sticker it does not sit in a bubble — it renders large (≈140pt) and naked directly on the chat backdrop, character art front and center, with only a tiny timestamp floating beside it. Conversations are punctuated by these big, expressive Brown/Cony/Sally moments, and the rest of the UI is deliberately calm so the stickers can carry the emotional weight.

The chat backdrop itself is a signature: a soft periwinkle blue (`#8CABD9`) by default (users can theme it), not white. Against that backdrop, incoming text bubbles are crisp white (`#FFFFFF`) and outgoing bubbles are LINE's pale chat-green (`#C5F0B1` — a soft, friendly mint, not the saturated brand green). The brand color, **LINE Green** (`#06C755`), is held for chrome that means "go": the active tab, primary buttons, the official-account verification badge, toggles, and the floating action affordances. The contrast of pale-green bubbles on periwinkle, interrupted by large color stickers, gives LINE a distinctly soft, playful, East-Asian-mobile feel — closer to a friendly scrapbook than a utility.

Typography is **Noto Sans** (LINE ships globally with deep CJK coverage; system fonts are the fallback) at weights 400 / 700. Type is functional and unobtrusive — friend names at 16pt, message text at 16pt, the sticker shop and official accounts using 700 for emphasis. The bottom tab bar is broad: Home, Talk, VOOM, News, Wallet — LINE is a super-app, and the tab bar advertises that breadth. Friend list rows, the official-account badge, and the sticker shop tile are recurring structural components. The overall atmosphere: soft, character-forward, friendly, and unmistakably built around stickers as the primary way people talk.

**Key Characteristics:**
- **Oversized sticker messages** — stickers render large (~140pt) and bubble-less directly on the chat backdrop
- Periwinkle chat backdrop (`#8CABD9`) by default — not white; users can theme it
- Outgoing bubble pale chat-green (`#C5F0B1`); incoming bubble white (`#FFFFFF`)
- LINE Green (`#06C755`) for "go" chrome — active tab, primary buttons, official-account badge, toggles
- Friend list rows with circular avatars + status message line
- Official-account badge — a green shield/diamond verifying brand accounts
- Sticker shop tile — a recurring entry point; stickers are a first-class economy
- Broad super-app tab bar: Home / Talk / VOOM / News / Wallet
- Noto Sans, weights 400/700, deep CJK coverage
- Sticker bounce-in motion — stickers arrive with a playful spring

## 2. Color Palette & Roles

### Primary
- **LINE Green** (`#06C755`): Active tab, primary buttons, official-account badge, toggles, send-ready accents, FAB.
- **LINE Green Pressed** (`#05A647`): Pressed/active state for green buttons and toggles.
- **Outgoing Bubble Green** (`#C5F0B1`): The pale chat-green of your own message bubbles (soft mint, NOT the brand green).

### Canvas & Surface (Light)
- **Chat Backdrop** (`#8CABD9`): The default periwinkle chat-room background (themeable).
- **Canvas White** (`#FFFFFF`): Friend list, Home, settings, sheets, incoming bubbles, nav bars.
- **Surface Gray** (`#F7F7F7`): Grouped sections, search field, sticker-shop cards background.
- **Divider** (`#E5E5E5`): Hairline dividers between list rows.
- **Text Primary** (`#000000`): Friend names, message text, primary labels.
- **Text Secondary** (`#8C8C8C`): Status messages, timestamps, previews, metadata.
- **Text Tertiary** (`#B3B3B3`): Placeholders, disabled labels.

### Canvas & Surface (Dark — Limited Use)
LINE's iOS app is primarily a light/themeable surface; a dark variant exists for system-dark users.
- **Dark Canvas** (`#1E1E1E`)
- **Dark Surface** (`#2A2A2A`)
- **Dark Chat Backdrop** (`#1A2230`) (dark periwinkle)
- **Dark Incoming Bubble** (`#2A2A2A`)
- **Dark Text Primary** (`#EDEDED`)
- **Dark Text Secondary** (`#9A9A9A`)

### Message-Specific
- **Incoming Bubble** (`#FFFFFF`): Left-aligned received text bubble.
- **Outgoing Bubble** (`#C5F0B1`): Right-aligned sent text bubble (pale green).
- **Bubble Text** (`#000000`): Text in both incoming and outgoing bubbles.
- **Bubble Meta** (`#8C8C8C`): Timestamp + read ("Read") beside a bubble (sits outside the bubble, on the backdrop).
- **Read Label** (`#8C8C8C`): The small "Read" text under/next to your last outgoing message.

### Semantic
- **Official Account Green** (`#06C755`): The verification badge on official/business accounts.
- **Sticker Shop Accent** (`#06C755`): "Sticker Shop" entry, "Get" buttons on free stickers.
- **Notification Red** (`#FF334B`): Unread badge dots and counts.
- **Link Blue** (`#1F8FFF`): Inline links inside text bubbles.
- **Error Red** (`#FF334B`): Failed-to-send, destructive actions.

## 3. Typography Rules

### Font Family
- **Primary**: `Noto Sans` (LINE ships globally; deep Latin + CJK + Thai coverage)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Hiragino Sans', 'Noto Sans CJK', 'Segoe UI', Roboto, sans-serif`
- **Weights**: 400 (regular), 700 (bold) — LINE's hierarchy is mostly regular vs bold, no semibold tier

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Noto Sans | 24pt | 700 | 1.2 | 0pt | "Home", "Talk" nav title |
| Friend Name | Noto Sans | 16pt | 400 | 1.3 | 0pt | Row title in friend / chat list |
| Thread Title | Noto Sans | 17pt | 700 | 1.25 | 0pt | Contact/group name in chat header |
| Message Body | Noto Sans | 16pt | 400 | 1.45 | 0pt | The text-bubble content — core reading size |
| Status Message | Noto Sans | 13pt | 400 | 1.3 | 0pt | Friend's status line under their name |
| Message Preview | Noto Sans | 14pt | 400 | 1.3 | 0pt | Last-message snippet in chat list |
| Section Header | Noto Sans | 13pt | 700 | 1.2 | 0.2pt | "Friends", "Groups", grouped headers |
| Timestamp | Noto Sans | 12pt | 400 | 1.2 | 0pt | Time on a row, or beside a bubble/sticker |
| Read Label | Noto Sans | 11pt | 400 | 1.2 | 0pt | "Read" indicator beside last outgoing |
| Button (Primary) | Noto Sans | 16pt | 700 | 1.0 | 0pt | Green primary button labels |
| Button (Text) | Noto Sans | 15pt | 700 | 1.0 | 0pt | Green text actions |
| Tab Label | Noto Sans | 10pt | 400 | 1.0 | 0pt | Bottom tab bar labels |
| Badge Count | Noto Sans | 11pt | 700 | 1.0 | 0pt | Unread count in a red badge |
| Sticker Price | Noto Sans | 13pt | 700 | 1.0 | 0pt | Coin price on sticker-shop tiles |

### Principles
- **Two weights, regular and bold**: Noto Sans 400/700 — LINE's hierarchy is contrast by weight, no semibold step
- **Stickers replace expressive type**: there is no display headline because the *sticker* is the expressive element
- **Message body at 16pt / line-height 1.45**: comfortable for Latin and CJK alike
- **CJK-first metrics**: line-height is set generously so CJK glyphs are not cramped
- **Dynamic Type respected** on names and bodies; tab labels, badge counts, sticker prices are layout-fixed
- **Bold means "tappable/important"**: official-account names, sticker-shop CTAs, primary buttons

## 4. Component Stylings

### Buttons

**Primary Button ("Add Friend", "Get", "Confirm")**
- Background: `#06C755`
- Text: `#FFFFFF`, Noto Sans 16pt weight 700
- Padding: 13pt vertical, full-width (16pt horizontal inset) or pill on smaller actions
- Corner radius: 8pt (rectangular) or 22pt (pill, for inline "Add"/"Get")
- Pressed: `#05A647`, scale 0.98

**Outline Button ("Cancel", secondary)**
- Background: `#FFFFFF`
- Text: `#06C755`, Noto Sans 16pt weight 700
- Border: 1pt solid `#06C755`
- Corner radius: 8pt
- Pressed: background `#F1FBF3`

**Text Button ("Edit", "More")**
- Noto Sans 15pt weight 700, color `#06C755`
- No background; 44pt hit area; pressed opacity 0.5

**Send Button**
- When composer empty: a sticker/emoji + mic + plus glyph cluster (no send)
- When text exists: a green up-arrow inside a `#06C755` circle 30pt, white glyph 16pt
- Pressed: `#05A647`, scale 0.92

**Icon Action (Album, Camera, Sticker, Voice, Plus)**
- Glyph 24pt, hit area 44pt
- Default: `#8C8C8C`
- The sticker (smiley) glyph opens the sticker keyboard — the most-used action

### Cards & Containers

**Friend / Chat List Row**
- Height: 68pt
- Layout: 52pt circular avatar (leading) → name (16pt w400) + status/preview (13–14pt w400 `#8C8C8C`, 1-line clamp) → trailing: timestamp (12pt) + unread red badge
- Official account: a green badge (shield) sits immediately right of the name
- Background: `#FFFFFF`; pressed `#F7F7F7`
- Divider: hairline `#E5E5E5` inset to text origin

**Text Bubble — Incoming**
- Background: `#FFFFFF`
- Text: `#000000`, Noto Sans 16pt w400
- Padding: 9pt vertical, 12pt horizontal
- Corner radius: 16pt with a small 4pt notch at the top-left near the avatar (LINE's tail)
- Sender avatar (40pt) shown leading on the first message of a run; sender name (12pt `#8C8C8C`) above first bubble in groups
- Max width: 70% of chat width; left-aligned with 8pt leading inset after avatar
- Timestamp + nothing-or-"Read" floats just outside the bubble's bottom corner (on the backdrop), 11–12pt `#8C8C8C`

**Text Bubble — Outgoing**
- Background: `#C5F0B1` (pale chat-green)
- Text: `#000000`, Noto Sans 16pt w400
- Corner radius: 16pt with a 4pt notch at the top-right (tail)
- Meta: "Read" + timestamp floats outside the bubble's bottom-left, 11–12pt `#8C8C8C`
- Max width: 70%; right-aligned with 8pt trailing inset

**Oversized Sticker Message (Signature)**
- NOT in a bubble — the sticker image renders directly on the chat backdrop
- Size: ~140pt on the long edge (animated/sound stickers slightly larger), preserving aspect
- Alignment: right for outgoing, left for incoming (after avatar for incoming)
- Meta: timestamp (+ "Read") floats beside the sticker's lower edge, 11pt `#8C8C8C`, no background plate
- Entrance: a bounce-in spring (scale 0.6 → 1.08 → 1.0) — stickers feel like they "land"
- Tapping an animated sticker replays its animation/sound

**Official Account Badge (Signature)**
- A small rounded-diamond/shield glyph in `#06C755` (white inner mark)
- Sits inline immediately after an official account's display name in lists, headers, and chat
- Communicates verified/business identity — LINE's trust primitive

**Sticker Shop Tile**
- Card on `#F7F7F7`, corner radius 12pt
- Sticker preview image (square), title (Noto Sans 14pt w700), author (12pt `#8C8C8C`)
- Trailing: a pill price button — green outline with coin icon + price (Noto Sans 13pt w700) or "Free"

**Status / System Note**
- Centered, full-width on the backdrop, Noto Sans 12pt w400 `#FFFFFF` on a translucent `rgba(0,0,0,0.18)` rounded plate
- "Brown joined the group", date separators ("Today")

### Navigation

**Bottom Tab Bar (Super-App)**
- Height: 50pt + safe area
- Background: `#FFFFFF`, top hairline `#E5E5E5`
- Tabs: Home, Talk, VOOM, News, Wallet
- Icon 24pt; active `#06C755`, inactive `#8C8C8C`
- Labels: Noto Sans 10pt w400, always shown
- Talk tab: unread total red badge `#FF334B`; News/VOOM may show a red dot

**Chat List / Home Top Bar**
- Title (24pt w700) leading; trailing cluster: search + add-friend + settings glyphs 22pt `#000000`
- Search field below (where shown): `#F7F7F7` fill, 36pt, corner radius 18pt, placeholder "Search"

**Thread Header**
- Height: 44pt + safe area, `#FFFFFF` background, bottom hairline
- Leading: back chevron 22pt `#000000` + name (17pt w700) + official badge (if applicable)
- Trailing: audio-call + video-call + menu (☰) glyphs 22pt `#000000`, 16pt gap
- Tapping the menu opens the chat settings/album drawer

### Input Fields

**Composer**
- Sits on the chat backdrop; the field is a white pill
- Leading on the backdrop: plus glyph 24pt `#FFFFFF` (collapses the actions)
- Pill: `#FFFFFF`, min height 36pt, corner radius 18pt
- Field: Noto Sans 16pt w400, placeholder "Aa", trailing-inside: a smiley (sticker) glyph and a camera glyph
- Outside the pill: mic glyph when empty; green send circle when text exists
- The smiley glyph is prominent — the sticker keyboard is a primary input mode

**Search Field**
- Background: `#F7F7F7`
- Height: 36pt, corner radius 18pt
- Leading magnifier 16pt `#8C8C8C`, placeholder "Search" `#B3B3B3`
- Focus: cursor `#06C755`

### Distinctive Components

**Oversized Sticker Message**
- The defining element: bubble-less, ~140pt, character art on the periwinkle backdrop, bounce-in
- A LINE conversation is recognizable from a single sticker moment alone

**Official Account Badge**
- Green verification mark inline after a name — LINE's identity/trust signal across the super-app

**Sticker Shop**
- A first-class destination with tiles, prices in coins, free packs — stickers are an economy, not a feature

**Periwinkle Chat Backdrop**
- The non-white default chat background is itself a brand signature; pale-green bubbles + white bubbles are tuned to read on it

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52
- Bubble inner padding: 9pt vertical, 12pt horizontal
- Inter-bubble gap: 3pt within a same-sender run, 10pt between senders
- Sticker vertical margin: 10pt above/below (it needs air to read as a moment)
- Row inset: avatar leading 16pt, text origin 76pt

### Grid & Container
- Single column for chat and lists
- Text bubble max width: 70% of chat width
- Sticker max edge: ~140pt (does not obey the 70% bubble rule — it's bubble-less)
- Home is a vertical scroll of grouped sections (Friends, Groups, Services, Banner)
- iPad: master-detail split (list left, chat right)

### Whitespace Philosophy
- **Stickers need air**: generous vertical margin around a sticker so it reads as an expressive beat
- **Calm chrome, loud content**: the UI is soft and quiet so character stickers can be the focal point
- **Backdrop is part of the layout**: bubbles and meta are tuned for legibility on periwinkle, not white
- **Super-app density on Home**: Home packs many service entries, but Talk stays breathing-room calm

### Border Radius Scale
- Notch (4pt): the small tail notch on a bubble's avatar-side top corner
- Standard (8pt): primary/outline buttons, sticker-shop CTAs (rect)
- Card (12pt): sticker-shop tiles, Home service cards
- Bubble (16pt): text message bubbles
- Pill (18pt+): search field, composer pill, inline "Add"/"Get" buttons
- Circle (50%): avatars, send button, FAB

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Lists, Home cards, the chat backdrop |
| Bubble (Level 1) | `rgba(0,0,0,0.06) 0 1px 2px` | Text bubbles get a faint lift so white/green read on periwinkle |
| Sticker (Level 0) | No shadow | Stickers sit directly on the backdrop, flat and naked |
| Bar Hairline (Level 1) | `rgba(0,0,0,0.05) 0 0.5px 0` | Tab bar + thread header |
| Sheet (Level 2) | `rgba(0,0,0,0.16) 0 -8px 28px` | Sticker keyboard, attachment sheet, menus |
| Toast (Level 3) | `rgba(0,0,0,0.18) 0 6px 18px` | "Saved", "Added" confirmations |

**Shadow Philosophy**: The chat backdrop is colored, not white, so text bubbles take a *very* faint 1px shadow to lift them legibly off the periwinkle — without that, white-on-periwinkle would feel like it's bleeding. Stickers, by contrast, are deliberately flat and shadowless: they're meant to feel like they're drawn directly onto the conversation. Real elevation is reserved for the sticker keyboard and sheets.

### Motion
- **Sticker bounce-in**: a newly arrived sticker scales 0.6 → 1.08 → 1.0 with a spring (damping 0.55) — the signature motion
- **Animated/sound sticker tap**: replays the sticker's animation/audio with a small re-bounce
- **Text bubble enter**: subtle fade + 6pt rise, 220ms
- **Send circle press**: scale 1.0 → 0.92 → 1.0, soft haptic
- **Sticker keyboard present**: slides up 280ms ease, the field's smiley glyph toggles to a keyboard glyph
- **Tab switch**: instant crossfade
- **Read receipt**: "Read" fades in beside the bubble when delivered-read

## 7. Do's and Don'ts

### Do
- Render stickers **bubble-less and oversized** (~140pt) directly on the chat backdrop, with bounce-in
- Use the periwinkle `#8CABD9` chat backdrop by default (themeable) — not white
- Make outgoing bubbles pale chat-green `#C5F0B1`, incoming white `#FFFFFF`, text black
- Reserve LINE Green `#06C755` for "go" chrome: active tab, primary buttons, official badge, toggles
- Float timestamp + "Read" *outside* the bubble on the backdrop, not inside it
- Show the official-account green badge inline after verified names everywhere
- Give the sticker keyboard a primary, prominent smiley entry in the composer
- Use Noto Sans 400/700 with generous CJK-friendly line-height (1.45 on body)

### Don't
- Don't put stickers in a bubble or cap them at the 70% bubble width — they're bubble-less and big
- Don't use the saturated brand green `#06C755` for the outgoing bubble — that's the pale `#C5F0B1`
- Don't default the chat background to white — the periwinkle backdrop is a brand signature
- Don't put a shadow on stickers — they sit flat on the backdrop; only text bubbles get a faint lift
- Don't bury official-account verification — the green badge is the trust primitive
- Don't add a semibold tier — LINE's hierarchy is 400 vs 700 only
- Don't cramp CJK — keep line-height generous
- Don't hide the breadth — the super-app tab bar (Home/Talk/VOOM/News/Wallet) is intentional

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 60pt rows, sticker ~120pt, bubble max 76% |
| iPhone 13/14/15 | 390pt | Standard — 68pt rows, sticker ~140pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the header |
| iPhone 15/16 Pro Max | 430pt | Larger avatars (56pt), sticker ~160pt |
| iPad | 768pt+ | Master-detail split: list (left, 360pt) + chat (right) |

### Dynamic Type
- Friend names, message bodies, status messages: full scale
- Message body caps at ~22pt; bubble grows with content
- Stickers: fixed size (image asset, not type-driven)
- Tab labels, badge counts, sticker prices: layout-fixed (do not scale)

### Orientation
- Lists / chat / Home: **portrait + landscape** (landscape widens chat; sticker keeps ~140pt)
- iPad: split view in both orientations
- Image/video viewer: rotates freely

### Touch Targets
- Send circle: 30pt visual, 44pt effective hit
- Sticker (tap to replay animation): the full ~140pt image is tappable
- List rows: full 68pt row tappable
- Tab items: 24pt icon, 49pt hit
- Composer smiley/sticker glyph: 44pt hit (it's a primary action)

### Safe Area Handling
- Top: title/header respect safe area / Dynamic Island
- Bottom: composer + tab bar respect home indicator; sticker keyboard rises above the home indicator
- Sides: 8pt chat insets (bubbles), 16pt list insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Chat backdrop: `#8CABD9` (periwinkle, themeable)
- Canvas (lists/Home): `#FFFFFF`
- Surface: `#F7F7F8` / `#F7F7F7`
- Incoming bubble: `#FFFFFF`; outgoing bubble: `#C5F0B1` (pale green); bubble text: `#000000`
- Divider: `#E5E5E5`
- Text primary: `#000000`; secondary: `#8C8C8C`; tertiary: `#B3B3B3`
- LINE Green (chrome/CTA/badge): `#06C755`; pressed `#05A647`
- Notification red: `#FF334B`; link blue: `#1F8FFF`

### Example Component Prompts
- "Create a SwiftUI LINE friend list row: white background, 68pt height, 52pt circular avatar leading, name 'Brown' Noto Sans 16pt weight 400 #000000 with a #06C755 official-account badge inline right of the name, status message 'Out of office until Monday' Noto Sans 13pt #8C8C8C (1-line clamp). Trailing: timestamp '9:41' Noto Sans 12pt #8C8C8C. Pressed background #F7F7F7. Divider hairline #E5E5E5 inset to text origin."
- "Build a LINE oversized sticker message — NOT in a bubble. The sticker image renders directly on the #8CABD9 chat backdrop at ~140pt on the long edge, right-aligned for outgoing. A timestamp '9:41' (+ 'Read') Noto Sans 11pt #8C8C8C floats just beside the sticker's lower-left edge with NO background plate. Entrance: a bounce-in spring scale 0.6 → 1.08 → 1.0 (damping 0.55). Tapping an animated sticker replays it."
- "Design LINE text bubbles on the periwinkle backdrop: incoming bubble #FFFFFF, outgoing bubble #C5F0B1 (pale chat-green, NOT the brand green), text #000000 Noto Sans 16pt w400, padding 9pt/12pt, corner radius 16pt with a 4pt tail notch on the avatar-side top corner, faint shadow rgba(0,0,0,0.06) 0 1px 2px so they lift off the periwinkle. Meta ('Read' + time) floats OUTSIDE the bubble on the backdrop, 11pt #8C8C8C."
- "Create a LINE sticker-shop tile: card on #F7F7F7, corner radius 12pt, square sticker preview, title 'Brown & Friends' Noto Sans 14pt w700, author 'LINE' Noto Sans 12pt #8C8C8C, and a trailing green-outline pill price button with a coin glyph + '120' Noto Sans 13pt w700 (or 'Free')."
- "Build the LINE super-app bottom tab bar: 50pt + safe area, white, top hairline #E5E5E5. Five tabs Home / Talk / VOOM / News / Wallet, icons 24pt, active #06C755, inactive #8C8C8C, labels Noto Sans 10pt w400. Talk shows a #FF334B unread badge with a white count."

### Iteration Guide
1. Stickers are **bubble-less and oversized** (~140pt) on the chat backdrop with a bounce-in — this is THE brand
2. The chat backdrop is periwinkle `#8CABD9` by default (themeable), never white
3. Outgoing bubble = pale chat-green `#C5F0B1`; incoming = white; text always black — do NOT use brand green for bubbles
4. LINE Green `#06C755` is reserved for "go" chrome: active tab, primary buttons, official badge, toggles
5. Timestamp + "Read" floats OUTSIDE the bubble on the backdrop, not inside
6. The official-account green badge is the trust primitive — show it inline after verified names
7. Make the composer's sticker (smiley) glyph a primary, prominent input — stickers are how people talk
8. Noto Sans 400/700 only, generous CJK-friendly line-height; the super-app tab bar breadth is intentional
