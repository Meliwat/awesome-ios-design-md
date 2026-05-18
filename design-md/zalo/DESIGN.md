# Design System Inspiration of Zalo (iOS)

## 1. Visual Theme & Atmosphere

Zalo is Vietnam's #1 messaging app and a full super-app — chat, calls, social timeline (Nhật ký), Zalo Pay, Official Accounts (OA), and a launcher of mini-apps and services. Its iOS design system is built around one anchor color — **Zalo Blue (`#0068FF`)** — applied with total consistency: every screen header is a solid Zalo Blue bar, the primary CTA is Zalo Blue, the active tab is Zalo Blue, the send button is a Zalo Blue circle, and outgoing message bubbles carry a soft Zalo-blue tint. This relentless blue is the brand: open any of Zalo's many surfaces and the blue header instantly says "Zalo." The content canvas is clean white (`#FFFFFF`); the chat thread sits on a calm blue-grey backdrop (`#E8ECF1`) so the white incoming bubbles and tinted outgoing bubbles read with clear separation.

The two signature surfaces are the **chat thread** and the **discovery / mini-app hub**. The chat thread is a classic messenger pattern executed with Zalo's specifics: a Zalo Blue header (back chevron + circular avatar + name + presence + call/video actions), a scrolling thread of **asymmetric bubbles** — incoming bubbles are white with a clipped bottom-left tail corner (16pt radius, 5pt on the tail), outgoing bubbles are `#DBEBFF` (soft Zalo-blue) with a clipped bottom-right tail — day separators as centered pills, inline image bubbles, "Đã xem / Seen" receipts, and a white composer with a Zalo Blue send circle. The discovery/mini-app hub is a grid of rounded-square service icons (Zalo Pay, top-up, calendar, gifts, photos, OA, services) — the super-app launcher that makes Zalo more than a chat app.

There is **no decorative secondary palette** — Zalo is monochromatically blue plus functional semantics. The notification/badge red is `#F5325B` (unread counts, like hearts on the timeline). Success is green `#18A957` (payment confirmed), warning amber `#FF9500`. Mini-app icons each get a saturated tile color (blue, green, orange, red, violet, cyan) but these are contained to the launcher grid — they don't leak into chrome. The interface is dense and utilitarian by Vietnamese super-app convention: lots of list rows, tight spacing, clear hierarchy, function over flourish.

Typography uses **Be Vietnam Pro** (Google Fonts) — a sans designed specifically for Vietnamese with proper diacritic spacing (the stacked tone marks ậ ệ ọ ữ need vertical room). This is critical: a generic Latin font clips Vietnamese diacritics; Be Vietnam Pro is built for them. On iOS it falls back to SF Pro (which handles Vietnamese acceptably). Hierarchy is messenger-conventional: a 24pt screen title, 17pt list titles (contact/chat names), 15pt body and bubble text, 13pt message previews, 11pt timestamps.

**Key Characteristics:**
- Single anchor color — Zalo Blue (`#0068FF`) on every header, primary CTA, active tab, send button, outgoing bubble tint
- White content canvas (`#FFFFFF`) + calm blue-grey chat backdrop (`#E8ECF1`)
- Asymmetric chat bubbles — white incoming (clipped bottom-left tail), blue-tinted outgoing (clipped bottom-right tail), 16pt radius / 5pt tail
- Mini-app / service launcher grid — rounded-square saturated icon tiles (super-app hub)
- Solid Zalo Blue header bar with circular avatar + presence + call/video actions
- "Đã xem / Seen" read receipts + centered day-separator pills
- Be Vietnam Pro typeface — built for Vietnamese diacritics (never clip tone marks)
- Bottom tab bar: Tin nhắn / Danh bạ / Khám phá / Nhật ký / Cá nhân with red unread badges
- Notification red (`#F5325B`) for unread counts & timeline likes; green/amber semantics
- Dense, utilitarian list-row layouts — super-app function over flourish
- Social timeline (Nhật ký) — Facebook-like feed inside the messaging app
- Zalo Pay surfaces — financial flows with the same blue chrome

## 2. Color Palette & Roles

### Primary (Brand Anchor)
- **Zalo Blue** (`#0068FF`): The brand. Header bars, primary CTA, active tab, send button, links, selected states.
- **Zalo Blue Pressed** (`#0052CC`): Pressed state for blue controls.
- **Zalo Blue Deep** (`#0047B3`): Header gradient bottom, avatar gradients, deep accents.
- **Bubble Out** (`#DBEBFF`): Soft Zalo-blue tint of outgoing message bubbles.
- **Bubble Out Ink** (`#14223A`): Text inside outgoing bubbles (deep navy for contrast on the soft tint).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary content canvas, incoming bubbles, composer, tab bar, list rows.
- **Chat Backdrop** (`#E8ECF1`): Calm blue-grey behind the chat thread.
- **Surface 1** (`#F4F5F7`): Search field, grouped settings, composer field.
- **Surface 2** (`#EBEDF0`): Pressed rows, segmented control track.
- **Divider** (`#E4E6EB`): Hairline dividers between list rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#15171A`): Primary content canvas on dark.
- **Dark Chat Backdrop** (`#101418`): Behind the chat thread on dark.
- **Dark Bubble In** (`#1F2329`): Incoming bubble on dark.
- **Dark Bubble Out** (`#0A3A7A`): Outgoing bubble (deep Zalo-blue tint) on dark, white text.
- **Dark Divider** (`#262A30`): Hairline dividers on dark.

### Text
- **Ink / Text Primary** (`#1A1A1A`): Names, titles, incoming bubble text, primary content.
- **Text Secondary** (`#6B7280`): Message previews, presence, metadata.
- **Text Tertiary** (`#9AA0AA`): Timestamps, placeholders, disabled.
- **Text on Blue** (`#FFFFFF`): All text/icons on the Zalo Blue header.
- **Text Primary Dark** (`#ECEDEF`): Primary text on dark.
- **Text Secondary Dark** (`#9AA0AA`): Metadata on dark.

### Semantic & Accent
- **Notification Red** (`#F5325B`): Unread badges, timeline like hearts, destructive actions.
- **Success Green** (`#18A957`): Payment confirmed, friend-added toast.
- **Warning Amber** (`#FF9500`): Warnings, pending states.
- **Mini-App Tile Colors**: Blue `#0068FF`, Green `#18A957`, Orange `#FF9500`, Red `#F5325B`, Violet `#7B5CFF`, Cyan `#00B8D4` — contained to the launcher grid only.
- **Presence Online** (`#18A957`): "Active now" dot.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Header | `#0068FF` | `#0068FF` |
| Canvas | `#FFFFFF` | `#15171A` |
| Chat backdrop | `#E8ECF1` | `#101418` |
| Bubble in | `#FFFFFF` | `#1F2329` |
| Bubble out | `#DBEBFF` | `#0A3A7A` |
| Divider | `#E4E6EB` | `#262A30` |
| Text primary | `#1A1A1A` | `#ECEDEF` |
| Text secondary | `#6B7280` | `#9AA0AA` |
| Notification red | `#F5325B` | `#F5325B` |

## 3. Typography Rules

### Font Family
- **Primary**: `Be Vietnam Pro` (by Ltogether / Bùi Diệp, Google Fonts, SIL OFL) — a sans designed specifically for Vietnamese with correct diacritic spacing (stacked tone marks need vertical room). The right choice for a Vietnamese-first super-app.
- **iOS Fallback**: `-apple-system, 'SF Pro Text'` — SF Pro handles Vietnamese diacritics acceptably when Be Vietnam Pro isn't bundled.
- **Fallback Stack**: `'Be Vietnam Pro', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Be Vietnam Pro | 32pt | 800 | 1.25 | -0.5pt | Discover / large hero header |
| Screen Title | Be Vietnam Pro | 24pt | 700 | 1.3 | -0.3pt | "Tin nhắn", nav title |
| Section | Be Vietnam Pro | 20pt | 700 | 1.3 | -0.2pt | Section headers, group titles |
| List Title | Be Vietnam Pro | 17pt | 600 | 1.35 | 0pt | Contact / chat name, header name |
| Body / Bubble | Be Vietnam Pro | 15pt | 400 | 1.45 | 0pt | Message bubble text, body copy |
| Cell Title | Be Vietnam Pro | 14pt | 600 | 1.35 | 0pt | Settings row, mini-app group title |
| Preview | Be Vietnam Pro | 13pt | 400 | 1.4 | 0pt | Last-message preview in chat list |
| Button | Be Vietnam Pro | 15pt | 700 | 1.0 | 0pt | Primary / soft buttons |
| Timestamp | Be Vietnam Pro | 11pt | 600 | 1.3 | 0pt | "09:28 · Đã xem", message time |
| Tab Label | Be Vietnam Pro | 10pt | 500 | 1.0 | 0pt | Bottom tab labels (Vietnamese) |
| Badge | Be Vietnam Pro | 9pt | 700 | 1.0 | 0pt | Unread count number |
| Mini-App Label | Be Vietnam Pro | 11pt | 500 | 1.2 | 0pt | Launcher icon caption |

### Principles
- **Vietnamese-first metrics**: line heights are deliberately generous (1.35-1.45) so stacked diacritics (ậ ệ ọ ữ ằ) never clip. Never tighten the line box to fit a single-line cell — let it grow.
- **Be Vietnam Pro everywhere**: one family across the whole super-app; weight (400/500/600/700/800) carries hierarchy, not face changes.
- **Bubble text is 15pt regular**: chat is the core surface — body and bubble share 15pt w400 for reading comfort at conversational density.
- **Names are 17pt semibold**: contact/chat names and the chat-header name are the scannable anchors of the list-heavy UI.
- **Dynamic Type first-class**: titles, body, bubbles, previews scale; timestamps, badges, tab labels, mini-app captions stay fixed (layout-sensitive in dense rows and the launcher grid).

## 4. Component Stylings

### Buttons

**Primary Button (Gửi / primary CTA)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#0068FF` (Zalo Blue)
- Text: `#FFFFFF`, Be Vietnam Pro 15pt weight 700
- Padding: 13pt vertical, 28pt horizontal
- Pressed: background `#0052CC` + scale 0.98
- Disabled: `#0068FF` at 40% opacity

**Soft Button (Kết bạn / secondary)**
- Background: `#DBEBFF` (soft Zalo-blue tint)
- Text: `#0068FF`, Be Vietnam Pro 15pt weight 700
- Corner radius: 8pt, padding 13pt / 28pt
- Pressed: background `#C5DEFF`

**Outline Button (Hủy / cancel)**
- Background: transparent
- Text: Ink (`#1A1A1A`) light / `#ECEDEF` dark
- Border: 1pt `#D8DBE0` light / `#404550` dark
- Corner radius: 8pt, padding 11pt / 22pt
- Font: Be Vietnam Pro 14pt weight 600

**Follow / Action Pill (Theo dõi)**
- Background: `#0068FF`, fully rounded (500pt)
- Text: `#FFFFFF`, Be Vietnam Pro 13pt weight 700
- Padding: 8pt / 20pt
- Following state: soft — `#DBEBFF` bg, `#0068FF` text

**Send Button**
- Circular, 38pt, `#0068FF` fill, white paper-plane glyph (18pt)
- When composer empty: shows mic/camera glyphs instead in Zalo Blue stroke

**Text Link Button**
- Be Vietnam Pro 14pt weight 600, color `#0068FF`
- No underline; pressed: 70% opacity

### Core Atoms

**Chat Bubble (the signature atom)**
- Incoming: `#FFFFFF` bg, `#1A1A1A` text, 16pt radius with **bottom-left tail corner at 5pt** (asymmetric), soft shadow `0 1px 2px rgba(0,0,0,0.06)`, aligned left, max 78% width
- Outgoing: `#DBEBFF` bg, `#14223A` text, 16pt radius with **bottom-right tail corner at 5pt**, no shadow, aligned right, max 78% width
- Padding: 9pt vertical, 13pt horizontal
- Image bubble: 5pt padding around a 14pt-radius image
- Timestamp: 10pt `#9AA0AA` inside/below bubble; outgoing right-aligned
- Consecutive same-sender bubbles tighten to 2pt gap; avatar shows only on the last incoming of a run

**Day Separator**
- Centered pill, `rgba(255,255,255,0.7)` bg, `#6B7280` text, Be Vietnam Pro 11pt, 999pt radius, 3pt/12pt padding ("Hôm nay · Today")

**Read Receipt**
- Right-aligned below the last outgoing bubble, "Đã xem · Seen 09:27", Be Vietnam Pro 10pt `#9AA0AA`

**Avatar**
- Circle: 38pt in chat header, 28pt in thread, 48pt in chat list, 56pt on profile
- Online dot: 10pt `#18A957` with 2pt white ring, bottom-right
- Fallback: linear-gradient(135deg, `#4D9FFF`, `#0047B3`) with initials

**Chat List Row**
- 72pt tall: 48pt avatar (+ online dot) + name (17pt w600) over preview (13pt `#6B7280`, line-clamp 1) + right column (time 11pt `#9AA0AA`, unread badge)
- Unread: name w700, preview `#1A1A1A`, red badge
- 0.5pt `#E4E6EB` bottom divider, inset to avatar trailing edge

**Unread Badge**
- Red `#F5325B`, min 16pt circle (pill if >9), white Be Vietnam Pro 9pt w700, top-right of icon/row

**Mini-App Tile**
- 52pt rounded-square (15pt radius), saturated tile color, white 24pt glyph centered
- Caption below: Be Vietnam Pro 11pt w500, line-clamp 2, centered

### Navigation

**Top Header (Zalo Blue bar)**
- Height: 44pt + safe area, solid `#0068FF` (subtle gradient to `#0047B3` optional)
- Chat header: back chevron + circular avatar + name/presence stack + call + video icons (all white)
- List header: large title (white) + search + add icons; search can expand into a field below
- All header glyphs/text are `#FFFFFF`

**Bottom Tab Bar**
- Height: 52pt + safe area, `#FFFFFF` bg, 0.5pt top border `#E4E6EB`
- Tabs (5): Tin nhắn, Danh bạ, Khám phá, Nhật ký, Cá nhân
- Icon size: 23pt; Active: filled, `#0068FF`; Inactive: outline, `#9AA0AA`
- Labels: Be Vietnam Pro 10pt w500 (Vietnamese), always shown
- Unread badge: red `#F5325B` top-right of the Tin nhắn icon

### Input Fields

**Search Bar**
- Height: 38pt, `#F4F5F7` bg (or white-on-blue inside header), 19pt radius (capsule)
- Leading magnifyingglass 16pt `#9AA0AA`
- Placeholder "Tìm kiếm" Be Vietnam Pro 14pt `#9AA0AA`
- Focus: 1.5pt `#0068FF` border

**Message Composer**
- White bar, 0.5pt top divider; leading emoji/sticker glyph (Zalo Blue 23pt)
- Field: `#F4F5F7` capsule, 38pt, "Tin nhắn" placeholder 13pt `#9AA0AA`
- Trailing: image/camera glyph (Zalo Blue) -> swaps to a `#0068FF` send circle when text present
- Attachment tray expands above on the paperclip/plus

### Distinctive Components

**Chat Thread**
- Calm `#E8ECF1` backdrop, asymmetric bubbles, day pills, image bubbles, seen receipts, typing indicator (3 animated dots in a white bubble)

**Mini-App / Service Launcher Grid (Khám phá)**
- 4-column grid of 52pt rounded-square tiles + captions; section headers ("Tiện ích", "Dịch vụ"); the super-app surface

**Social Timeline (Nhật ký)**
- Facebook-like feed: poster row (avatar + name + time + privacy) -> text -> photo grid -> reaction bar (Thích / Bình luận / Chia sẻ); like uses the red heart `#F5325B`

**Zalo Pay Card**
- A Zalo Blue gradient card with balance + quick actions (Nạp tiền / Chuyển tiền / Quét mã) — financial flows in the same blue chrome

**Official Account (OA) Header**
- A verified-badge header (blue check) + Follow pill + message button — businesses inside chat

**Presence & Typing**
- "Active now" green dot + text under the name; typing = 3-dot animated bubble in incoming style

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 5 (bubble tail / image pad), 6 (bubble run gap), 8, 12, 16, 20, 24, 32
- Chat thread horizontal inset: 12pt
- List row content inset: 16pt
- Bubble inner padding: 9pt vertical, 13pt horizontal
- Launcher grid: 4 columns, 16pt gap

### Grid & Container
- Chat list / settings: full-width 72pt (chat) / 56pt (settings) rows, divider inset to content
- Chat thread: 12pt insets, bubbles max 78% width
- Launcher: 4-column grid (5 on Pro Max), 16pt gap, centered tiles
- iPad: 2-pane (chat list left ~360pt + thread right); launcher 6-8 columns

### Whitespace Philosophy
- **Utilitarian density**: this is a super-app — list rows are tight (72pt chat, 56pt settings), information-dense, scannable. Whitespace serves legibility, not luxury.
- **Chat breathing room**: the thread is the exception — generous bubble padding and a calm backdrop make conversation comfortable
- **Blue framing**: the solid blue header is a constant frame; content below is clean white — the contrast is the structure

### Border Radius Scale
- Square (0pt): header bar, full-bleed media
- Subtle (5pt): bubble tail corner, image-in-bubble inset
- Standard (8pt): buttons, cards, inputs (rect)
- Comfortable (12pt): sheets, attachment tray, dialogs
- Bubble (16pt): chat bubbles (3 corners; 4th is the 5pt tail)
- Tile (15pt): mini-app launcher tiles
- Capsule (19-20pt): search field, composer field
- Pill (500pt): action pills, day separators, unread badges
- Circle (50%): avatars, send button, online dots

## 6. Depth & Elevation

Zalo is mostly flat with light, functional elevation — it's a dense utility app, not a showcase.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, launcher tiles, timeline cards, outgoing bubbles |
| Hairline (Level 0.5) | 0.5pt `#E4E6EB` border/divider | Row dividers, tab-bar top, composer top |
| Bubble (Level 1) | `rgba(0,0,0,0.06) 0 1px 2px` | Incoming chat bubbles (subtle lift off the backdrop) |
| Floating (Level 2) | `rgba(0,0,0,0.10) 0 4px 16px` | FAB / new-chat button, attachment tray |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -4px 24px` | Bottom sheets (forward, react, attachment, sticker picker) |
| Modal Overlay | `rgba(0,0,0,0.45)` scrim | Behind modals, image viewer, call screen |

**Shadow Philosophy**: The only routinely-visible shadow is the very soft `0 1px 2px` on incoming white bubbles so they separate from the `#E8ECF1` backdrop; outgoing tinted bubbles need no shadow (the color separates them). Everything else relies on hairline dividers and the solid blue header for structure. In dark mode the bubble shadow is dropped (incoming `#1F2329` separates from `#101418` by value) and dividers carry the structure.

### Motion
- **Bubble send**: outgoing bubble scales 0.9 → 1.0 + fades in over 200ms ease-out, nudging the thread up; soft haptic
- **Bubble receive**: incoming bubble fades + 6pt slide-up over 200ms; typing indicator (3 dots) bounces in a loop then morphs into the bubble
- **Read receipt**: "Đã xem" fades in under the last outgoing bubble over 150ms
- **Tab change**: instant content swap; the active icon crossfades outline → filled and tints Zalo Blue over 120ms
- **Header transition**: standard iOS push (300ms horizontal); the blue bar persists across pushes (no color flash)
- **Launcher tile press**: scale 1.0 → 0.94 over 100ms, springs back; opening a mini-app pushes with a 280ms slide
- **Pull to refresh** (timeline/list): Zalo Blue circular spinner
- **Send-success / payment**: green `#18A957` checkmark pops (200ms) for Zalo Pay confirmations
- **Reaction picker**: long-press a bubble -> a row of emoji scales in over 180ms with a soft haptic
- **Haptic feedback**: soft on send, light tick on tab change, medium on payment confirm / friend add

## 7. Do's and Don'ts

### Do
- Use Zalo Blue (`#0068FF`) as the single anchor — every header, primary CTA, active tab, send button
- Keep the content canvas white (`#FFFFFF`) and the chat thread on the calm blue-grey backdrop (`#E8ECF1`)
- Make chat bubbles asymmetric: white incoming with a clipped bottom-left tail, blue-tinted outgoing with a clipped bottom-right tail (16pt / 5pt tail)
- Use Be Vietnam Pro and give Vietnamese diacritics generous line height — never clip tone marks
- Build the mini-app / service launcher as a 4-column grid of rounded-square saturated tiles
- Use the red `#F5325B` for unread badges and timeline likes only
- Show "Đã xem / Seen" receipts and centered day-separator pills in the thread
- Keep list rows dense and scannable (72pt chat, 56pt settings) — it's a super-app
- Persist the solid blue header across navigation pushes (no color flash)
- Use soft blue (`#DBEBFF`) for secondary buttons and the "following" pill state
- Contain saturated mini-app tile colors to the launcher grid only

### Don't
- Don't introduce a second brand color — Zalo is monochromatically blue + functional semantics
- Don't make both bubbles symmetric — the asymmetric clipped-tail corner is the signature
- Don't put a green/grey bubble for outgoing — outgoing is the soft Zalo-blue `#DBEBFF`
- Don't use a generic Latin font that clips Vietnamese diacritics — use Be Vietnam Pro / SF Pro
- Don't tighten line boxes to force single-line cells — diacritics need vertical room
- Don't let mini-app tile colors leak into headers, tabs, or chrome
- Don't use a white or grey header — the Zalo Blue bar is the brand frame
- Don't add heavy shadows — Zalo is flat with hairline dividers + one soft bubble shadow
- Don't use the notification red for anything but unread counts, likes, and destructive actions
- Don't over-space list rows — super-app density is intentional, function over flourish
- Don't drop the green online dot / presence text — presence is core to the messenger

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Chat rows 68pt; launcher 4-col tight; bubble max 80% |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island sits over the blue header (status text white) |
| iPhone 15/16 Pro Max | 430pt | Launcher 5-col; larger avatars; bubble max 76% |
| iPad (portrait) | 768pt | 2-pane: chat list (~360pt) + thread; launcher 6-col |
| iPad (landscape) | 1024pt+ | Persistent 2-pane; launcher 8-col; thread centered max 720pt |

### Dynamic Type
- Display, screen titles, list titles, body, bubbles, previews: full scale (line heights grow to keep diacritics safe)
- Timestamps, unread badges, tab labels, mini-app captions: FIXED (layout-sensitive)
- Chat rows grow vertically with Dynamic Type rather than truncating diacritics

### Orientation
- Chat thread rotates; bubbles re-wrap; image bubbles keep aspect
- iPad landscape: persistent split (list + thread)
- Launcher grid recomputes column count on rotation/width

### Touch Targets
- Chat list row: full 72pt row tappable
- Bubble: full bubble tappable (long-press = react/forward menu), > 44pt
- Send button: 38pt circle, 44pt hit
- Tab bar icons: 23pt glyph, 48pt hit
- Launcher tile: 52pt tile + caption, 64pt effective hit

### Safe Area Handling
- Top: blue header extends under the status bar / Dynamic Island; status text is white
- Bottom: tab bar / composer respect the home indicator
- Sides: thread keeps 12pt insets; list rows 16pt; image viewer edge-to-edge
- Keyboard: composer docks above the keyboard; thread scrolls to the last bubble on keyboard show

## 9. Agent Prompt Guide

### Quick Color Reference
- Zalo Blue: `#0068FF` (pressed `#0052CC`, deep `#0047B3`)
- Canvas: `#FFFFFF` light / `#15171A` dark
- Chat backdrop: `#E8ECF1` light / `#101418` dark
- Bubble in: `#FFFFFF` (text `#1A1A1A`) / dark `#1F2329`
- Bubble out: `#DBEBFF` (text `#14223A`) / dark `#0A3A7A` (white text)
- Divider: `#E4E6EB` light / `#262A30` dark
- Text primary: `#1A1A1A` / `#ECEDEF` dark
- Text secondary: `#6B7280` · tertiary `#9AA0AA`
- Notification red: `#F5325B` · Success `#18A957` · Warning `#FF9500`
- Mini-app tiles: blue `#0068FF`, green `#18A957`, orange `#FF9500`, red `#F5325B`, violet `#7B5CFF`, cyan `#00B8D4`

### Example Component Prompts
- "Build the Zalo chat thread in SwiftUI: a `#E8ECF1` backdrop with a scrolling list of asymmetric bubbles. Incoming: `#FFFFFF` bg, `#1A1A1A` text, 16pt corner radius with the bottom-LEFT corner at 5pt (the tail), soft shadow `0 1px 2px rgba(0,0,0,0.06)`, left-aligned, max 78% width, 28pt avatar on the last bubble of a run. Outgoing: `#DBEBFF` bg, `#14223A` text, 16pt radius with the bottom-RIGHT corner at 5pt, no shadow, right-aligned. 9pt/13pt padding, 6pt gap (2pt within a same-sender run). Centered day-separator pills (`rgba(255,255,255,0.7)`, 11pt `#6B7280`) and a right-aligned 'Đã xem · Seen' receipt (10pt `#9AA0AA`)."

- "Create the Zalo chat header: a solid `#0068FF` bar (44pt + safe area) with a white back chevron, a 38pt circular avatar, a name/presence stack (name Be Vietnam Pro 15pt w600 white, 'Active now' 11pt white 85%), and trailing white call + video icons (21pt). The bar persists across navigation pushes with no color flash."

- "Render the Zalo message composer: a white bar with a 0.5pt `#E4E6EB` top divider. Leading sticker/emoji glyph in `#0068FF` (23pt). A `#F4F5F7` capsule field (38pt, 19pt radius) with placeholder 'Tin nhắn' Be Vietnam Pro 13pt `#9AA0AA`. Trailing image/camera glyph in Zalo Blue that swaps to a 38pt `#0068FF` send circle (white paper-plane) when text is present. Soft haptic on send; outgoing bubble scales 0.9 → 1.0 + fades in over 200ms."

- "Build the Zalo mini-app launcher grid: a 4-column grid of 52pt rounded-square tiles (15pt radius) in saturated colors (Zalo Pay blue `#0068FF`, top-up green `#18A957`, calendar orange `#FF9500`, gifts red `#F5325B`, photos violet `#7B5CFF`, OA cyan `#00B8D4`), each with a centered white 24pt glyph and a caption below in Be Vietnam Pro 11pt w500 (line-clamp 2). 16pt grid gap, section headers in Be Vietnam Pro 20pt w700. Tile press: scale 1.0 → 0.94 over 100ms."

- "Create the Zalo bottom tab bar: 52pt + safe area, `#FFFFFF` bg, 0.5pt top border `#E4E6EB`. Five tabs: Tin nhắn, Danh bạ, Khám phá, Nhật ký, Cá nhân. Active = filled icon tinted `#0068FF`; inactive = outline `#9AA0AA`. Labels Be Vietnam Pro 10pt w500. A red `#F5325B` unread badge (min 16pt circle, white 9pt w700) sits top-right of the Tin nhắn icon."

- "Render the Zalo chat list row: 72pt tall, 16pt inset. 48pt circular avatar with a 10pt `#18A957` online dot (2pt white ring). Name in Be Vietnam Pro 17pt w600 `#1A1A1A` over a 1-line preview in 13pt `#6B7280`. Right column: timestamp 11pt `#9AA0AA` and (if unread) a red badge. Unread state bolds the name to w700 and the preview to `#1A1A1A`. 0.5pt `#E4E6EB` divider inset to the avatar's trailing edge."

### Iteration Guide
1. One anchor color: Zalo Blue `#0068FF` on every header, primary CTA, active tab, send button, outgoing bubble tint — never add a second brand color
2. White content canvas `#FFFFFF`; the chat thread sits on calm blue-grey `#E8ECF1`
3. Bubbles are ASYMMETRIC: white incoming (clipped bottom-left 5pt tail), blue-tinted `#DBEBFF` outgoing (clipped bottom-right 5pt tail), 16pt elsewhere
4. Use Be Vietnam Pro and give Vietnamese diacritics generous line height (1.35-1.45) — never clip tone marks
5. The mini-app / service launcher (4-col rounded-square saturated tiles) is the super-app signature alongside chat
6. The solid Zalo Blue header is the constant frame — it persists across navigation pushes, no color flash
7. Notification red `#F5325B` is for unread badges, timeline likes, and destructive actions only
8. List rows are dense and scannable (72pt chat / 56pt settings) — super-app function over flourish
9. Only routine shadow is a soft `0 1px 2px` on incoming white bubbles; everything else is hairline dividers
10. Show presence (green dot + "Active now") and "Đã xem / Seen" receipts — they're core to the messenger
