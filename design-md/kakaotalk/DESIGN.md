# Design System Inspiration of KakaoTalk (iOS)

## 1. Visual Theme & Atmosphere

KakaoTalk's iOS app is the everyday super-app of South Korea — messaging at its core, with gifting, payments, channels, and shopping wrapped around it. Its design language is instantly identifiable by one thing: **Kakao Yellow (`#FEE500`)**, a saturated, almost-electric yellow that paints the outbound chat bubble, the primary CTA, and the rounded speech-bubble logo, always paired with a deep, near-black **Kakao Brown (`#3C1E1E`)** for the text that sits on that yellow. This yellow-and-brown pairing is the brand's whole personality: warm, playful, friendly, and unmistakably Korean. The app itself stays restrained — clean light backgrounds, a calm light-gray chat thread, plain system text — so the yellow can do the talking exactly where it matters.

Two screens anchor the product. The **Friends tab** is a vertically scrolling roster: your own profile banner at the top (rounded-square avatar, name, a one-line status message that often includes emoji), then a "Friends · N" section header, then friend rows — each a rounded-square avatar, name in semibold, and a status line (a mood, a now-playing track via the music badge, or "출근중..." style text). Below friends come Channels (Kakao's brand/business accounts). The **chat thread** is KakaoTalk's heart: inbound bubbles in light gray on the left with the sender's rounded-square avatar and small name above, outbound bubbles in Kakao Yellow on the right, and — the signature detail — the **unread-count number and timestamp docked to the inner side of the bubble**, not inside it. That little yellow "1" next to a bubble (meaning "1 person hasn't read this yet") is one of the most culturally recognizable UI elements in Korea.

KakaoTalk's other defining content type is **emoticons and gifticons**. Emoticons are Kakao Friends characters (Ryan the lionless-lion, Apeach, Muzi, Tube) sent as large stickers — they're a core revenue and expression channel, not an afterthought. Gifticons are sendable gift vouchers (a coffee, a cake, a chicken) that arrive as a rich card in the thread with a product image, brand, and name; the recipient redeems it with a barcode. The composer reflects this: a `+`/menu icon (to send gifticons, photos, money, location, schedules), a pill text field, and an emoticon-face icon that opens the Kakao Friends sticker keyboard.

Typography is the iOS system face (SF Pro) for Latin and the platform Korean face (Apple SD Gothic Neo) for Hangul; KakaoTalk does use a heavier "KakaoBig"-style weight for big titles. Hierarchy is utilitarian: bold nav titles, semibold list names, regular message body, small gray status/metadata. The type stays quiet and lets the yellow, the rounded-square avatars, and the Kakao Friends characters carry the brand.

**Key Characteristics:**
- Kakao Yellow (`#FEE500`) outbound bubbles + primary CTA + speech-bubble logo, on Kakao Brown (`#3C1E1E`) text
- Calm canvas — light gray/white light mode, warm charcoal `#1A1A1A` dark; a distinct light-gray chat thread backdrop
- Unread count + timestamp docked to the bubble's **inner side** — the yellow "1" is iconic in Korea
- Rounded-square avatars (≈16pt radius) — never circles — across friends, chat, and channels
- Friends tab: my-profile banner + "Friends · N" section + friend rows with status/now-playing
- Emoticons = Kakao Friends characters (Ryan / Apeach / Muzi / Tube) as large stickers — first-class
- Gifticons — sendable gift vouchers that arrive as a rich product card with a barcode redeem
- Channels — Kakao brand/business accounts listed below friends
- Bottom tab bar: Friends / Chats / Open (Chat) / Shopping / More — super-app surface area
- Status message line on profiles (a mood + emoji) — the social texture of Korean messaging
- Bubbles are gently rounded (≈14pt) with the head corner squared to ≈4pt

## 2. Color Palette & Roles

### Primary (Interactive)
- **Kakao Yellow** (`#FEE500`): The brand. Outbound message bubbles, primary CTA buttons, speech-bubble logo, unread count mark, key highlights.
- **Kakao Yellow Pressed** (`#E6CF00`): Pressed/active state on yellow buttons and bubbles.
- **Kakao Brown** (`#3C1E1E`): Text and glyphs that sit on yellow — logo fill, button label on yellow, outbound bubble text.
- **Kakao Brown Soft** (`#5A3A3A`): Secondary brown for icons/labels on lighter yellow surfaces.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Friends list, settings, modal backgrounds.
- **Chat Backdrop Light** (`#B2C7DA`): KakaoTalk's signature blue-gray chat-thread background (the classic "KakaoTalk wallpaper" default).
- **Surface Gray** (`#F5F5F5`): Grouped settings backdrop, search field fill.
- **Inbound Bubble Light** (`#FFFFFF`): Inbound message bubble fill on the blue-gray thread.
- **Divider Light** (`#E6E6E6`): Hairline dividers between list rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1A1A1A`): Friends/list canvas — NOT pure black; keeps the app warm.
- **Chat Backdrop Dark** (`#1E2A33`): Dark equivalent of the signature blue-gray thread color.
- **Dark Surface 1** (`#242424`): Composer bar, grouped settings rows, cards.
- **Dark Surface 2 / Inbound Bubble Dark** (`#2E2E2E`): Inbound bubble fill, hovered rows.
- **Dark Divider** (`#333333`): Hairline dividers.

### Text
- **Text Primary Light** (`#191919`): Primary list and message text on light.
- **Text Secondary Light** (`#6A6A6A`): Status messages, timestamps, metadata.
- **Text Tertiary Light** (`#9A9A9A`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#EDEDED`): Primary text on dark.
- **Text Secondary Dark** (`#9A9A9A`): Secondary text on dark.
- **Text On Yellow** (`#3C1E1E`): Text inside outbound bubbles and on yellow buttons (Kakao Brown).
- **Text On Thread (dark)** (`rgba(255,255,255,0.78)`): Sender names / day separators over the dark thread backdrop.

### Semantic
- **Unread Yellow** (`#FEE500`): The "N unread" count mark docked beside a bubble — the iconic accent.
- **Link Blue** (`#4B9BFF`): Hyperlinks, "see all" actions, selectable text.
- **Success Green** (`#2ECC71`): Success toasts, payment confirmation.
- **Error Red** (`#F15E6C`): Failed send, destructive actions (Leave chat, Block).
- **Notification Red** (`#FF3B30`): Tab-bar unread badge dot/count.
- **Music Badge** (`#6A6A6A`): Now-playing badge on a friend's status line.

### Kakao Friends Character Accents (emoticon brand palette)
| Character | Accent |
|-----------|--------|
| Ryan (lion) | `#FBC02D` |
| Apeach (peach) | `#FF9EB1` |
| Muzi (radish) | `#FFE082` |
| Tube (duck) | `#FFD54F` |
| Frodo (dog) | `#8D6E63` |
| Neo (cat) | `#A1887F` |

## 3. Typography Rules

### Font Family
- **Latin Primary**: `SF Pro Text` / `SF Pro Display` (Apple system font).
- **Hangul Primary**: `Apple SD Gothic Neo` (the iOS Korean system face) — KakaoTalk renders Korean text in the platform Korean font.
- **Big Titles**: KakaoTalk uses a heavier "KakaoBig"-style weight (mapped to SF Pro Display / Apple SD Gothic Neo Heavy on iOS) for large nav titles.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Apple SD Gothic Neo', 'SF Pro Text', 'Noto Sans KR', 'Segoe UI', Roboto, sans-serif`
- **Emoji / Emoticons**: Apple Color Emoji for system emoji; Kakao Friends emoticons are image stickers (not font glyphs).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display / SD Gothic Heavy | 32pt | 900 | 1.15 | -0.4pt | Big screen title (e.g. "친구") |
| Screen Title | SF Pro Display | 26pt | 700 | 1.2 | -0.3pt | Modal / sheet titles |
| Nav Title | SF Pro Display | 20pt | 900 | 1.2 | -0.3pt | Compact nav bar title ("Friends") |
| Profile Name | SF Pro Text | 17pt | 700 | 1.25 | -0.1pt | My-profile banner name |
| Section Header | SF Pro Text | 18pt | 700 | 1.3 | -0.1pt | "Members & settings" |
| Message Body | SF Pro Text | 15pt | 400 | 1.35 | 0pt | The message text inside bubbles |
| List Row Title | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Friend / chat name |
| Status / Preview | SF Pro Text | 13pt | 400 | 1.3 | 0pt | Status message, last-message preview |
| Sender / Tab | SF Pro Text | 12pt | 500 | 1.2 | 0.1pt | Name above an inbound bubble; tab labels |
| Unread Mark | SF Pro Text | 11pt | 700 | 1.0 | 0pt | The yellow "N" beside a bubble |
| Bubble Time | SF Pro Text | 10pt | 400 | 1.0 | 0pt | Timestamp docked beside a bubble |
| Tab Badge | SF Pro Text | 10pt | 700 | 1.0 | 0pt | Red unread count on a tab |
| Button | SF Pro Text | 16pt | 700 | 1.0 | 0pt | Primary button label |
| Emoticon-only | — | sticker | — | — | — | Kakao Friends stickers render ~120pt image |

### Principles
- **System faces, heavier titles**: Latin in SF Pro, Hangul in Apple SD Gothic Neo; only big titles go very heavy (900) for the friendly KakaoBig feel.
- **Body is 400, names are 600**: Message text is regular; friend/chat names are semibold for fast scanning.
- **Korean-first metrics**: Line height is generous (≈1.35) so Hangul (which has taller glyphs than Latin) breathes; never tighten below 1.3 for body.
- **Quiet type, loud yellow**: Hierarchy leans on weight + the yellow accent + rounded-square avatars, not big type contrast.
- **Dynamic Type first-class**: Message body, list names, status lines scale; tab labels, unread marks, bubble times stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (확인 / Confirm / Send gift)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FEE500` (Kakao Yellow)
- Text: `#3C1E1E` (Kakao Brown), SF Pro Text 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#E6CF00` + scale 0.98
- Disabled: `#FEE500` at 40% opacity

**Secondary / Outline Button (Add friend / Cancel)**
- Background: transparent
- Border: 1pt divider (`#E6E6E6` light / `#333333` dark)
- Text: primary text color, SF Pro Text 14pt weight 600
- Corner radius: 8pt
- Padding: 12pt vertical, 20pt horizontal
- Pressed: 6% gray fill

**Pill / Utility Button (선물하기 Gift / Mute / Filter)**
- Background: surface gray / `#2E2E2E` dark
- Border: 1pt divider
- Text: primary text color, SF Pro Text 14pt weight 500
- Corner radius: 999pt (pill)
- Padding: 10pt vertical, 18pt horizontal

**Text Button (전체 보기 / See all)**
- Font: SF Pro Text 14pt weight 600
- Color: `#4B9BFF` (Link Blue)
- No background, no underline
- Pressed: 50% opacity

### Message Bubble (the core atom)

**Inbound Bubble**
- Background: `#FFFFFF` (light, on the blue-gray thread) / `#2E2E2E` (dark)
- Text: primary text color, SF Pro Text 15pt weight 400
- Corner radius: 14pt, with the **top-leading corner squared to 4pt** (head/tail toward the avatar)
- Padding: 9pt vertical, 12pt horizontal
- Max width: 82% of thread width
- Leading avatar: 36pt rounded-square (≈14pt radius), aligned to the top of the bubble run
- Sender name: 12pt weight 500, `rgba(255,255,255,0.78)` on dark thread / `#6A6A6A` on light, above the first bubble in a run
- Meta (to the trailing side of the bubble, bottom-aligned): timestamp 10pt; no unread mark on inbound

**Outbound Bubble**
- Background: `#FEE500` (Kakao Yellow)
- Text: `#3C1E1E` (Kakao Brown), SF Pro Text 15pt weight 500
- Corner radius: 14pt, with the top-trailing corner squared to 4pt
- Aligned trailing; no avatar for self
- Meta (to the **leading side** of the bubble, bottom-aligned): unread count mark (yellow `#FEE500` "N", weight 700, 11pt) stacked above the timestamp (10pt `rgba(255,255,255,0.5)` on dark)

**Bubble Run Grouping**
- Consecutive messages from the same sender within 60s collapse: avatar + name show once at the top; bubbles stack with 3pt gap; the meta (time / unread) shows only on the last bubble of the run

### Unread Mark (signature distinctive component)
- A small number rendered to the **inner side** of an outbound bubble (between the bubble and the screen edge), bottom-aligned
- Color: Kakao Yellow `#FEE500`, SF Pro Text 11pt weight 700
- Means "N recipients have not yet read this message"; disappears (or counts down in a group) as people read
- The single most culturally recognizable KakaoTalk UI element

### Gifticon Card (distinctive component)
- A rich card sent in the thread for a gift voucher
- Container: rounded 16pt, `#FFFFFF` light / `#242424` dark, soft shadow
- Top: product image band ~120pt tall (often a warm yellow gradient backdrop for the product)
- Body: brand name 11pt `#6A6A6A`, product name 13pt weight 700, "선물이 도착했어요" style header
- Footer (recipient side): "사용하기 / Use" pill button → opens a barcode redeem screen
- Width: ~188pt

### Navigation

**Top Nav (Friends / Chats)**
- Height: 52pt + safe area
- Background: canvas (`#FFFFFF` light / `#1A1A1A` dark)
- Leading: large/compact title (20pt weight 900)
- Trailing: a row of 21pt outline action icons (search, add friend, settings/⋮) `#6A6A6A`
- 0.5pt bottom divider

**Chat Nav Bar (top of a thread)**
- Height: 52pt + safe area
- Background: matches the thread backdrop (blue-gray light / `#1E2A33` dark)
- Leading: back chevron 22pt
- Center-leading: chat title (friend/group name) 16pt weight 700
- Trailing: search-in-chat + hamburger (chat menu / drawer) 20pt

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(255,255,255,0.96)` light / `rgba(26,26,26,0.96)` dark with blur + 0.5pt top divider
- Tabs (5): Friends, Chats, Open (Chat), Shopping, More
- Icon size: 24pt
- Active: primary text color (`#191919` / `#EDEDED`), icon filled — no color tint
- Inactive: `#777777`, icon stroked
- Unread badge: red `#FF3B30` pill on the Chats icon, white 10pt weight 700
- Labels: SF Pro Text 10pt weight 500, always shown

**Friend Row**
- Height: 64pt
- Leading: 44pt rounded-square avatar (≈16pt radius)
- Title: friend name 15pt weight 600
- Subtitle: status message 13pt weight 400 `#6A6A6A` (1 line) OR a now-playing badge (music note 13pt + track name)
- Trailing: optional gift / VIP / channel chip

### Input Fields

**Composer**
- Layout: `+` menu button (left) · pill text field (center) · emoticon-face button (right) · (send/voice morphs in on the right when typing)
- Pill field: height 38pt, background `#F5F5F5` light / `#2E2E2E` dark, 999pt radius, 16pt horizontal padding
- Placeholder: empty (KakaoTalk shows no placeholder text — just the empty pill)
- `+` button: 24pt outline glyph `#6A6A6A` — opens the action sheet (album, camera, gifticon, pay/송금, location, schedule, voice, file, contact)
- Emoticon button: 24pt smiley glyph `#6A6A6A` — opens the Kakao Friends sticker keyboard
- Bar background: canvas, 0.5pt top divider

**Search Field (Friends / Chats)**
- Height: 38pt
- Background: `#F5F5F5` light / `#2E2E2E` dark
- Corner radius: 999pt (pill) or 8pt (depending on screen)
- Leading icon: magnifyingglass 16pt `#9A9A9A`
- Placeholder: "이름, 채팅방, 모임 검색" 14pt `#9A9A9A`

### Distinctive Components

**Unread Count Mark** — see above; the iconic yellow "N" beside a bubble.

**Kakao Friends Emoticon Sticker**
- Sent as a large image sticker (~120pt) with no bubble background; tappable to enlarge/animate
- Emoticon keyboard: a grid of the user's owned sticker sets with character tabs along the bottom

**Gifticon Card** — see above; sendable gift voucher with barcode redeem.

**Status Message Line**
- A one-line mood under a profile name; supports emoji and updates over time — the social texture of the friends list

**My-Profile Banner**
- Top of the Friends tab: large rounded-square avatar + name + editable status message; tapping opens the full profile (cover image, decorations, music)

**Open Chat (오픈채팅) Tab**
- A discovery/list surface for public/anonymous group rooms by topic — its own bottom tab with a globe-style icon

**Now-Playing Badge**
- A music-note glyph + track title on a friend's status line, sourced from their profile music

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 16, 18, 20, 24, 28, 32
- Bubble vertical gap (different senders): 12pt
- Bubble vertical gap (same-sender run): 3pt
- Thread horizontal inset: 12pt
- List row inset: 18pt leading / 18pt trailing

### Grid & Container
- iPhone: chat thread full-bleed over the backdrop with 12pt horizontal inset; bubbles max 82% width
- iPad: thread max-width 680pt centered; split view shows the Chats list (340pt) + thread
- Friends list: full-width 64pt rows; section headers ("Friends · N", "Channels")
- Bottom tab bar pinned; nav bars pinned

### Whitespace Philosophy
- **Roster density**: friend rows are compact (64pt) so the eye scans names + statuses quickly
- **Thread breathing**: 12pt between speakers, 3pt within a run; the colored backdrop frames the bubbles
- **Avatars anchor the left rail**: inbound rows reserve a 36pt rounded-square avatar gutter so names + bubbles align
- **Meta sits outside the bubble**: time + unread count dock to the bubble's inner side so the bubble itself stays clean — a defining KakaoTalk layout choice

### Border Radius Scale
- Square (0pt): nav bars, dividers, full-screen media
- Subtle (4pt): the squared head corner of a bubble
- Standard (8pt): primary/secondary buttons, search field
- Bubble (14pt): message bubbles
- Avatar / Card (16pt): rounded-square avatars, gifticon cards, sheets
- Comfortable (24pt): bottom sheets
- Pill (999pt): composer field, utility chips, gifticon "Use" button
- Circle (50%): rarely used — small status dots only (avatars are rounded-squares, not circles)

## 6. Depth & Elevation

KakaoTalk is mostly flat with soft, subtle shadows; depth signals "this floats above the thread/list".

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Thread backdrop, list rows, bubbles |
| Raised (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` | Gifticon card, composer separation |
| Floating (Level 2) | `rgba(0,0,0,0.16) 0 6px 16px` | Action sheet, emoticon keyboard, popovers |
| Sheet (Level 3) | `rgba(0,0,0,0.22) 0 -4px 24px` | Bottom sheets (chat menu, gift flow, settings) |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind modals and sheets |

**Shadow Philosophy**: The thread and list stay flat so the yellow bubbles and rounded-square avatars carry the visual weight. Shadows are reserved for cards (gifticon) and floating UI (action sheet, emoticon keyboard). On dark mode shadows nearly vanish, so floating panels gain a 1pt `#333333` border as the elevation cue and cards switch to a `#242424` fill that lifts them off the `#1E2A33` thread / `#1A1A1A` canvas.

### Motion
- **Message send**: outbound bubble pops up + fades in from the composer over 200ms ease-out; the unread mark fades in once delivered
- **Unread countdown (group)**: the yellow "N" cross-fades to "N-1" as each member reads — no aggressive animation
- **New inbound message**: bubble fades + 6pt slide-up in 180ms ease-out; thread auto-scrolls if pinned to bottom
- **Emoticon send**: sticker scales 0.7 → 1.0 over 220ms spring (animated emoticons then play their loop)
- **Action sheet / emoticon keyboard**: slides up from bottom 280ms ease-out with scrim fade
- **Tab switch**: instant content swap; active icon cross-fades fill in 120ms; red badge bounces in
- **Gifticon arrival**: card slides in + a subtle confetti/sparkle accent on first appearance
- **Pull-to-refresh (Friends)**: Kakao-yellow spinner; 250ms settle
- **Haptic feedback**: light tick on send, soft impact on emoticon send, selection tick on tab change

## 7. Do's and Don'ts

### Do
- Use Kakao Yellow (`#FEE500`) for outbound bubbles, the primary CTA, and the speech-bubble logo
- Put Kakao Brown (`#3C1E1E`) text on yellow — never white or black
- Dock the unread count + timestamp to the bubble's **inner side**, not inside the bubble
- Render the unread mark as a yellow "N" — it is the single most iconic KakaoTalk element
- Use rounded-square avatars (≈16pt radius) everywhere — friends, chat, channels
- Give the chat thread its signature blue-gray backdrop (light) / `#1E2A33` (dark)
- Treat Kakao Friends emoticons as large stickers (no bubble) — they are first-class content
- Render gifticons as rich product cards with a barcode "Use" redeem
- Show a status-message line under profile/friend names (mood + emoji, or now-playing)
- Use Apple SD Gothic Neo for Hangul and generous line height (≈1.35) for Korean text
- Keep the list/thread flat; reserve soft shadows for cards and floating sheets

### Don't
- Don't use white or black text on the yellow bubble — it must be Kakao Brown `#3C1E1E`
- Don't put the time/unread count inside the bubble — KakaoTalk docks it to the inner side
- Don't use circular avatars — KakaoTalk avatars are rounded squares
- Don't color the chat thread plain white — it has a distinct blue-gray backdrop
- Don't render emoticons inside a bubble — they are bubble-less large stickers
- Don't tighten Hangul line height below ~1.3 — Korean glyphs need vertical room
- Don't tint the active tab yellow — active tabs use the primary text color (filled icon), no color tint
- Don't pure-black the dark canvas — use `#1A1A1A` (warm) and `#1E2A33` for the thread
- Don't substitute the yellow with orange/gold — it is specifically `#FEE500`
- Don't squarify bubbles — they're ≈14pt rounded with only the head corner clipped to ~4pt
- Don't bury the `+` menu — gifticons, pay, schedule, location all live there and are core to KakaoTalk

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max-width 84%; avatar 40pt; gifticon card 172pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; nav bar extends under island |
| iPhone 15/16 Pro Max | 430pt | Bubble max-width 80%; avatar 48pt; larger gifticon image |
| iPad (portrait) | 768pt | Split: Chats list (340pt) + thread (centered max 680pt) |
| iPad (landscape) | 1024pt+ | Persistent Friends/Chats list + thread; Shopping in a wider grid |

### Dynamic Type
- Scales: message body, list names, status lines, screen titles
- Fixed: tab labels, unread marks, bubble times, tab badges (layout-sensitive in run grouping)
- Gifticon card image scales proportionally with thread width, not font scale

### Orientation
- All screens rotate; iPad gains persistent split view (list + thread)
- Media/emoticon full-screen viewer supports rotation
- Composer + emoticon keyboard stay pinned above the keyboard in both orientations

### Touch Targets
- Bubble (long-press for reactions/menu): full bubble, min 44pt tall
- Tab bar icons: 24pt glyph, 44pt hit
- `+` menu / emoticon button: 24pt glyph, 44pt hit
- Friend row: full 64pt row tappable
- Gifticon "Use" pill: min 36pt tall

### Safe Area Handling
- Top: nav bars extend to the top safe area and under the Dynamic Island (content within safe area)
- Bottom: tab bar + composer respect the home indicator
- Keyboard: thread scrolls above the keyboard; composer + emoticon keyboard dock on it
- Sides: 12pt thread inset, 18pt list inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Kakao Yellow: `#FEE500`
- Yellow pressed: `#E6CF00`
- Kakao Brown (text on yellow): `#3C1E1E`
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#1A1A1A`
- Chat backdrop: `#B2C7DA` light / `#1E2A33` dark
- Inbound bubble: `#FFFFFF` light / `#2E2E2E` dark
- Outbound bubble: `#FEE500` (brown text)
- Text primary: `#191919` light / `#EDEDED` dark
- Text secondary: `#6A6A6A` light / `#9A9A9A` dark
- Unread mark: `#FEE500`
- Link blue: `#4B9BFF`
- Notification badge: `#FF3B30`
- Error red: `#F15E6C`

### Example Component Prompts
- "Build the KakaoTalk Friends top section in SwiftUI: a 52pt nav row (title 'Friends' SF Pro Display 20pt weight 900, trailing search/add/settings outline icons 21pt `#6A6A6A`), then a my-profile banner — 52pt rounded-square avatar (16pt radius, yellow gradient `#FEE500`→`#E6CF00`), name SF Pro Text 17pt weight 700, status message 13pt `#6A6A6A` with emoji — then a 'Friends · N' section label SF Pro Text 12pt weight 700 `#6A6A6A`."

- "Create a KakaoTalk inbound message row: a 36pt rounded-square avatar (14pt radius) on the leading side aligned to the top of the run, then a column — sender name SF Pro Text 12pt weight 500 `rgba(255,255,255,0.78)` (on the dark `#1E2A33` thread), then bubbles. Inbound bubble: `#2E2E2E` (dark) fill, SF Pro Text 15pt weight 400 `#EDEDED`, 14pt corner radius with the top-leading corner squared to 4pt, 9pt vertical / 12pt horizontal padding, max-width 82%. The timestamp (10pt `rgba(255,255,255,0.5)`) sits to the trailing side of the bubble, bottom-aligned."

- "Build the KakaoTalk outbound bubble with its iconic unread mark: a Kakao-Yellow `#FEE500` bubble with `#3C1E1E` text SF Pro Text 15pt weight 500, 14pt radius with the top-trailing corner squared to 4pt, aligned trailing. On the **leading** side of the bubble (between it and the screen edge), bottom-aligned, render a small column: the unread count as a yellow `#FEE500` '1' SF Pro Text 11pt weight 700, and below it the timestamp 10pt `rgba(255,255,255,0.5)`. The mark fades in once delivered and counts down as recipients read."

- "Render a KakaoTalk gifticon card: a 188pt-wide rounded-16pt card (`#242424` dark, soft shadow), a ~120pt product-image band on a warm yellow gradient at top, then body — brand name 11pt `#9A9A9A`, product name 13pt weight 700 `#EDEDED` — and (recipient side) a pill 'Use / 사용하기' button 999pt radius that opens a barcode redeem screen."

- "Build the KakaoTalk composer: a bottom bar on canvas with a 0.5pt top divider. Layout: a `+` menu button (24pt outline glyph `#6A6A6A`) on the left, an empty pill text field in the center (38pt tall, `#2E2E2E` dark fill, 999pt radius, 16pt horizontal padding, NO placeholder text), and an emoticon-face button (24pt smiley `#6A6A6A`) on the right; a send glyph morphs in on the right when the field has text. The `+` opens an action sheet (album, camera, gifticon, pay/송금, location, schedule, voice)."

- "Create the KakaoTalk friend row: 64pt tall, leading 44pt rounded-square avatar (16pt radius), title = friend name SF Pro Text 15pt weight 600, subtitle = either a status message 13pt weight 400 `#6A6A6A` (1 line) OR a now-playing badge (music-note glyph 13pt + track title). Optional trailing gift/VIP/channel chip."

- "Build the KakaoTalk bottom tab bar: 5 tabs — Friends, Chats, Open, Shopping, More — 24pt icons, active = primary text color with a FILLED icon (no color tint), inactive = `#777777` stroked. A red `#FF3B30` unread badge pill (white 10pt weight 700) sits on the Chats icon. Labels SF Pro Text 10pt weight 500 always shown. Backdrop `rgba(26,26,26,0.96)` (dark) with blur + 0.5pt top divider."

### Iteration Guide
1. Outbound bubbles are Kakao Yellow `#FEE500` with Kakao Brown `#3C1E1E` text — never white/black text on yellow
2. The unread count + timestamp dock to the bubble's INNER side (outside the bubble) — the yellow "N" is the #1 iconic element
3. Avatars are rounded squares (≈16pt radius) everywhere — never circles
4. The chat thread has a distinct backdrop: blue-gray light / `#1E2A33` dark — not plain white
5. Inbound bubbles are white (light) / `#2E2E2E` (dark); the head corner is clipped to ~4pt, radius ≈14pt
6. Kakao Friends emoticons are large bubble-less stickers; gifticons are rich product cards with barcode redeem
7. Hangul renders in Apple SD Gothic Neo with generous (~1.35) line height; big titles go weight 900 (KakaoBig feel)
8. Active tabs use the primary text color with a filled icon — no yellow tint on the tab bar
9. Canvas is white light / `#1A1A1A` dark (warm, not pure black)
10. The `+` composer menu holds the super-app: gifticon, pay/송금, schedule, location — don't bury it
