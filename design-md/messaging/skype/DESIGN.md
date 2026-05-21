# Design System Inspiration of Skype (iOS)

## 1. Visual Theme & Atmosphere

Skype is Microsoft's veteran video-and-voice-calling app, and its design DNA is unmistakably **call-first**. Long before it was a chat client it was *the* way to make free internet calls, and every screen still leads with that promise: the contacts list, the prominent call buttons in every conversation header, the dedicated Calls tab, and the full-screen video grid are the heart of the product. The visual identity is built on one signature color — **Skype Blue**, a bright cloud-cyan `#00AFF0` (with a deeper, text-safe `#0078D4` for filled surfaces). It's in the cloud logomark, the call FABs, the primary buttons, the tab selection, and the outgoing message bubble. The atmosphere is bright, friendly, and approachable: rounded shapes, soft gradients on avatars and video tiles, and a clean canvas.

The canvas is white (`#FFFFFF`) in light mode and a neutral near-black (`#16161A`) in dark mode — Skype's dark is a true neutral gray-black so the cloud blue stays vivid against it. Conversations are a vertical stack of rounded bubbles: outgoing in the deeper Skype Blue `#0078D4` with white text and a small ~4pt tail on the bottom-right; incoming in a light gray `#EBEBEF` (light) / `#2A2A30` (dark) with primary-color text and a tail on the bottom-left. Bubbles use a ~14pt radius. Below or beside each outgoing bubble sits a timestamp and a small delivery state ("Delivered" / "Seen"). The chat history is also a *call log*: every voice/video call appears as an inline **call card** — a rounded surface with a green phone glyph, "Group video call", duration, and participant count — interleaved with messages.

The other signature pillar is the **video call grid**. When a call is active, participants render as gradient-backed video tiles in an adaptive grid, with a small self-view, per-tile mic indicators, and a bottom control bar of large circular buttons: mute, video toggle, share, and a red end-call button (with a green accept button on incoming). Skype's call screen and contacts list are as central as its chat. Other distinctive components: presence dots (green = active, yellow = away, gray = offline) on every avatar, message **reactions** (a heart-led emoji strip on long-press that docks as a chip on the bubble), the contacts directory with search and "Active now", screen sharing, and the Meet Now / instant-meeting entry.

Typography is Microsoft's house style: **Segoe UI** (the Windows/Skype brand sans). On platforms without Segoe the closest free substitutes are **Inter** or **Open Sans** — clean, neutral, humanist. Hierarchy is product-UI: bold screen titles, semibold contact headlines, 15pt message body, small gray metadata. Color beyond blue is purposeful: green `#2DC26B` for accept-call and online presence, red `#E8364F` for end-call/destructive and unread badges, yellow `#FFC400` for away presence. Skype Blue is the only saturated brand surface; everything else is neutral chrome.

**Key Characteristics:**
- Skype Blue (`#00AFF0` bright / `#0078D4` deep) is the entire brand — call FABs, buttons, tabs, outgoing bubble
- White canvas (`#FFFFFF`) light / neutral near-black (`#16161A`) dark
- Rounded ~14pt bubbles: outgoing deep blue `#0078D4`, incoming gray (`#EBEBEF` / `#2A2A30`), ~4pt tail
- Call-first chrome — phone + video icons in every chat header, dedicated Calls tab, Contacts tab
- Inline call cards — call history interleaved in the chat as rounded cards (glyph + duration + participants)
- Video call grid — gradient participant tiles, self-view, per-tile mic, big circular control bar
- Presence dots on every avatar — green active (`#2DC26B`), yellow away (`#FFC400`), gray offline
- Message reactions — heart-led emoji strip on long-press, docks as a chip on the bubble
- Contacts directory — searchable, "Active now", the people-centric heart of the app
- Accept (green `#2DC26B`) / End (red `#E8364F`) call buttons; red unread badges
- Segoe UI brand sans (Inter / Open Sans substitute), friendly and rounded
- Bottom tab bar: Chats / Calls / Contacts / Notifications with bright-blue active tint

## 2. Color Palette & Roles

### Primary (Interactive)
- **Skype Blue** (`#00AFF0`): The brand. Call FABs, primary buttons, tab selection, send button, links, icons.
- **Skype Blue Deep** (`#0078D4`): Outgoing bubble, filled surfaces needing white-text contrast, pressed accents.
- **Skype Blue Pressed** (`#0091CC`): Pressed state of bright-blue controls.
- **Skype Cyan** (`#34C3FF`): Lighter accent, gradient top stop, links on dark, outline text.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Thread background, primary light canvas.
- **Incoming Bubble** (`#EBEBEF`): Light gray incoming bubble.
- **Surface Gray** (`#F4F4F6`): Grouped list background, search field, call cards on light.
- **Row Pressed** (`#E8E8EC`): Pressed conversation/list rows.
- **Divider** (`#E2E2E6`): Hairline separators between list rows and header.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#16161A`): Neutral near-black thread background.
- **Dark Surface 1** (`#1F1F24`): Nav bar, input bar, tab bar, elevated surfaces.
- **Dark Surface 2** (`#2A2A30`): Incoming bubble, call cards, control buttons, sheets.
- **Dark Divider** (`#34343C`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#1B1B1F`): Incoming bubble text, primary labels.
- **Text on Blue** (`#FFFFFF`): Outgoing bubble text; on bright `#00AFF0` buttons use `#00343F` deep-cyan-ink for AA.
- **Text Secondary** (`#6E6E78` light / `#A4A4AE` dark): Timestamps, list preview, contact subtitle.
- **Text Tertiary** (`#9A9AA4` light / `#6F6F79` dark): Disabled, placeholder, "Delivered" micro-meta.
- **Text Primary Dark** (`#F2F2F4`): Incoming bubble text and labels on dark.

### Presence
- **Active / Online** (`#2DC26B`): Green presence dot, also accept-call.
- **Away** (`#FFC400`): Yellow presence dot.
- **Do Not Disturb** (`#E8364F`): Red presence dot (shares the destructive red).
- **Offline / Invisible** (`#9A9AA4` ring): Gray hollow ring.

### Semantic
- **Accept / Success Green** (`#2DC26B`): Accept-call button, success toast, online.
- **End / Error Red** (`#E8364F`): End-call button, delete, destructive, unread count badge.
- **Link** (`#0078D4` light / `#4FBDF0` dark): Inline links inside bubbles.
- **Selection Highlight** (`#00AFF0` @ 18%): Text selection within a bubble.

### Presence Color Matrix

| State | Dot | Color |
|-------|-----|-------|
| Active / Online | filled | `#2DC26B` |
| Away | filled | `#FFC400` |
| Do Not Disturb | filled (minus glyph) | `#E8364F` |
| Offline | hollow ring | `#9A9AA4` |
| In a call | filled (phone glyph) | `#00AFF0` |

## 3. Typography Rules

### Font Family
- **Brand**: `Segoe UI` — Microsoft's house humanist sans (proprietary; not redistributable cross-platform).
- **Recommended Free Substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — closest neutral humanist; or `Open Sans` (SIL OFL) for a slightly warmer match.
- **Fallback Stack**: `-apple-system, 'Segoe UI', BlinkMacSystemFont, 'SF Pro Text', 'Inter', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: proportional by default; tabular in timestamps and call durations.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Segoe UI | 32pt | 800 | 1.2 | -0.4pt | "Chats" / "Calls" screen title |
| Title 1 | Segoe UI | 26pt | 700 | 1.2 | -0.3pt | Call summary / sheet headers |
| Title 3 | Segoe UI | 22pt | 700 | 1.25 | -0.2pt | Group name, large contact name |
| Headline | Segoe UI | 17pt | 700 | 1.3 | -0.2pt | Contact name in chat header / list row title |
| Body / Bubble | Segoe UI | 15pt | 400 | 1.32 | 0pt | Message text |
| Body Emphasis | Segoe UI | 15pt | 600 | 1.32 | 0pt | Sender name in group bubbles, mentions |
| List Preview | Segoe UI | 14pt | 500 | 1.35 | 0pt | Last-message preview in chat list |
| Footnote | Segoe UI | 13pt | 600 | 1.35 | 0pt | Call card meta ("12 min · 3 participants") |
| Caption | Segoe UI | 11pt | 600 | 1.35 | 0.1pt | Timestamp separators, presence ("Active now") |
| Button | Segoe UI | 16pt | 700 | 1.0 | 0pt | Primary / call buttons |
| Tab Label | Segoe UI | 11pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Badge | Segoe UI | 11pt | 700 | 1.0 | 0pt | Unread count badges |

### Principles
- **Friendly and clean**: Segoe/Inter at heavy 700/800 for titles & buttons feels confident and approachable.
- **Body stays light**: message body at 15pt regular keeps chat readable and dense.
- **Dynamic Type supported**: titles, headline, body, list previews scale; timestamps, tab labels, badges, presence text stay fixed.
- **White text on the deep blue bubble**: outgoing bubble text is `#FFFFFF`; on the bright `#00AFF0` button, text uses deep-cyan ink `#00343F` for WCAG AA.
- **Tracking near-zero**: Segoe/Inter are comfortable at default tracking; only tiny captions get a small positive value.

## 4. Component Stylings

### Buttons

**Primary Button**
- Shape: Pill / rounded rectangle, 22pt corner radius
- Background: `#00AFF0`
- Text: `#00343F` (deep-cyan ink for AA on bright blue), Segoe UI 16pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `#0091CC` + scale 0.98
- Disabled: `#00AFF0` @ 35% opacity

**Call Button (Call / Accept)**
- Shape: pill 22pt radius (or 56pt circle in call control bar)
- Background: `#2DC26B` (accept/call) — white icon+label
- Used in the chat-header call action and incoming-call accept

**End-Call Button**
- Background: `#E8364F`, white phone-down glyph, 56pt circle in the control bar / pill in sheets

**Outline Button (secondary)**
- Background: transparent
- Border: 1.5pt `#00AFF0`
- Text: `#0078D4` (light) / `#34C3FF` (dark), Segoe UI 15pt weight 700
- Corner radius: 22pt
- Pressed: `#00AFF0` @ 10% fill

**Text Button**
- Background: transparent
- Text: `#0078D4` / `#34C3FF`, Segoe UI 15pt weight 600 ("Share Screen", "Add People")
- Pressed: opacity 0.5

**Send Button (circular)**
- 36pt circle, background `#00AFF0`, paper-plane glyph `#00343F` 16pt
- Mic icon (voice message) when the field is empty

### Message Bubble (the core atom)

**Outgoing Bubble**
- Background: `#0078D4` (deep Skype Blue — text-safe)
- Text: `#FFFFFF`, Segoe UI 15pt w400
- Padding: 9pt vertical, 14pt horizontal
- Corner radius: 14pt, bottom-right corner at 4pt (tail) when last in a same-sender run
- Alignment: trailing, max width ~80% of thread
- Footer: timestamp 10pt + delivery state, right-aligned below

**Incoming Bubble**
- Background: `#EBEBEF` light / `#2A2A30` dark
- Text: `#1B1B1F` / `#F2F2F4`, Segoe UI 15pt w400
- Corner radius: 14pt, bottom-left corner at 4pt (tail)
- Alignment: leading; in groups, sender name in 15pt w600 `#0078D4` above the first bubble

**Grouping**
- Same sender consecutive: 5pt gap, only last bubble shows the tail
- Sender change: 8pt gap
- Day separator: centered 11pt caption, `secondaryLabel`, 8pt vertical margin

**States**
- Sending: clock micro-glyph, bubble ~70% opacity until ack
- Failed: red exclamation, tap to resend
- With reaction: a small chip docked at the bottom-near corner (overlapping -12pt)
- Edited: "Edited" in 11pt `tertiaryLabel` below the bubble
- Reply quote: a thin blue left-bar + quoted snippet inside the bubble

### Inline Call Card

- A rounded surface interleaved in the chat history for every voice/video call
- Background: `#F4F4F6` light / `#2A2A30` dark, 14pt corner radius, 10/14pt padding
- Leading: 30pt circular icon, tinted by outcome — green phone `#2DC26B` @ 18% bg (answered), red `#E8364F` (missed/declined)
- Title: "Group video call" / "Audio call" Segoe UI 13pt w700
- Subtitle: "12 min · 3 participants" / "Missed · Tap to call back" 11pt `secondaryLabel`
- Trailing (optional): a "Call back" pill button

### Video Call Grid

- Active-call screen: adaptive grid of participant tiles
- Tile: gradient-backed (avatar fallback) or live video, 14pt corner radius
- Name label bottom-left (11pt w600 white on a subtle scrim)
- Per-tile mic indicator top-right: 18pt circle, `rgba(0,0,0,0.45)` bg, white mic/mic-off glyph
- Self-view: smaller pinned tile (e.g., 62pt wide), bordered `#34343C`
- Control bar (bottom): row of 52–56pt circular buttons — mute, video toggle, share, more, and a red end-call (green accept on incoming) — on a translucent dark scrim

### Navigation

**Chat Header (call-first)**
- Height: 56pt + safe area, hairline `divider` bottom
- Leading: back chevron (`chevron.left` 17pt `#00AFF0`)
- Center-left: 36pt circular avatar with a presence dot + name (Segoe UI 15pt w700) stacked over presence/status ("Active now" 11pt `#2DC26B`)
- Trailing: phone icon + video icon (22pt `#00AFF0`) — call-first
- Tapping the header opens the contact/profile screen

**Chat List (Chats tab)**
- Large title "Chats" (32pt w800), search field, Meet Now / compose entry
- Row: 72pt — leading 52pt circular avatar with presence dot, title (17pt w700), preview (14pt `secondaryLabel`, 1-line), trailing timestamp (13pt) + unread count badge (red `#E8364F` pill)
- Swipe: Delete (red), More

**Contacts Tab**
- Searchable directory, sectioned A–Z; "Active now" group at the top
- Row: avatar + presence + name + status text; tap → profile with Chat / Audio / Video call actions

**Bottom Tab Bar**
- Height: 50pt + safe area, background `#FFFFFF` / `#1F1F24`, hairline top divider
- Tabs (4): Chats, Calls, Contacts, Notifications
- Icon 22pt; active = `#00AFF0` filled glyph + label; inactive = `#9A9AA4` / `#6F6F79`
- Labels: Segoe UI 11pt w600 always shown; unread badge on Chats/Notifications (red pill)

### Input Fields

**Compose Bar**
- Background: `#FFFFFF` / `#1F1F24`, hairline top divider, 8/16pt padding
- Leading: `+` 22pt attachment (camera, gallery, file, location, contact, poll)
- Center: pill field — 18pt radius, `#F4F4F6` / `#2A2A30` fill, placeholder "Type a message" `tertiaryLabel`
- Inner-trailing: emoji face glyph 22pt
- Trailing: 36pt circular send button `#00AFF0`; mic when empty (voice message)

**Search Bar**
- Height: 36pt, `#F4F4F6` / `#2A2A30`, 10pt radius
- Leading `magnifyingglass` 15pt `tertiaryLabel`, placeholder "Search Skype"

### Distinctive Components

**Video Call Grid + Control Bar** — gradient participant tiles, self-view, per-tile mic, big circular controls; Skype's beating heart.

**Inline Call Card** — call history *in the chat* as rounded cards; the chat is also a call log.

**Presence Dots** — green/yellow/red/gray on every avatar; people-status is everywhere in Skype.

**Reactions Strip** — long-press a bubble → heart-led emoji row (❤️ 👍 😂 😮 😢 😡); docks as a chip.

**Contacts Directory** — searchable people list with "Active now"; the people-first surface.

**Call Screen (incoming)** — full gradient/blurred-avatar background, large avatar, ringing label, accept (green) / decline (red) circular controls.

**Meet Now** — instant-meeting CTA that creates a shareable call link without adding contacts.

**Screen Share Tile** — a participant tile flagged "Sharing" with a distinct border treatment.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 44
- Same-sender bubble gap: 5pt
- Sender-change gap: 8pt
- Thread horizontal inset: 14pt, bubble max width ~80%
- Bubble internal padding: 9pt vertical / 14pt horizontal

### Grid & Container
- iPhone: full-width thread, 14pt gutters; bubble max ~80%
- Video grid: adaptive — 1 / 2 / 3-4 / 5+ participants tile layouts; self-view pinned
- iPad: split — chat/contacts list (~340pt) + conversation/call; content max ~700pt centered
- Headers, call cards, banners: full-width, safe-area aware

### Whitespace Philosophy
- **Friendly density**: 5pt within a run, 8pt on sender change — conversational, slightly airier than dense IM
- **Blue is the only saturated surface**: chrome is neutral so the bright blue and call FABs pop
- **Call-first whitespace**: header call icons, call cards, and the Calls/Contacts tabs always have room — calling is never buried
- **Video grid breathes**: tiles have consistent 8pt gutters and rounded corners for a calm, organized call

### Border Radius Scale
- Square (0pt): full-bleed video (rare; tiles are rounded)
- Tail (4pt): the pinched bubble corner on the sender's edge
- Subtle (8pt): search bar, list thumbnails
- Standard (14pt): bubbles, call cards, video tiles, reply-quote container
- Comfortable (18pt): input field, sheets, larger cards
- Pill (22pt / 500pt): primary/call buttons, badges
- Circle (50%): avatars, send button, call-control buttons, presence dots

## 6. Depth & Elevation

Skype is mostly flat with soft neutral shadows on floating surfaces and gradient fills on avatars/video tiles.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Bubbles, list rows, call cards, thread content |
| Raised (Level 1) | `rgba(0,0,0,0.10) 0 2px 8px` | Floating Meet Now FAB, raised cards |
| Floating (Level 2) | `rgba(0,0,0,0.16) 0 6px 20px` (light) / 1pt border on dark | Reactions strip, context menu, call-control scrim |
| Sheet (Level 3) | `rgba(0,0,0,0.20) 0 -4px 24px` | Attachment sheet, share, add-people |
| Modal Overlay | `rgba(0,0,0,0.5)` dim | Behind the incoming-call screen, modals, sheets |

**Shadow Philosophy**: Skype uses soft neutral shadows on floating surfaces (reactions, menus, FAB) so they clearly hover, while content (bubbles, call cards) stays flat. Avatars and video tiles use cloud-blue gradients as the "depth" expression instead of shadows. On dark mode shadows fade, so floating surfaces gain a faint 1pt `divider` border. The incoming-call screen uses a blurred/gradient backdrop for focus.

### Motion
- **Bubble send**: outgoing bubble slides up + scales 0.88 → 1.0 from the input field, `spring(response: 0.32, damping: 0.8)` + light haptic
- **Reactions strip**: long-press (~450ms) → strip scales 0.7 → 1.0 spring; selecting docks the chip with a pop + soft haptic
- **Call card insert**: fades + slides in (0.25s) when a call ends and the card is appended
- **Video tile join/leave**: tile scales 0.9 → 1.0 fade-in on join; fade-out + grid reflow (0.3s) on leave
- **Call connect**: ringing screen crossfades to the grid (0.3s); control bar rises with spring
- **Mute/video toggle**: button fill crossfades 0.15s + the affected tile's mic glyph updates instantly
- **Tab switch**: instant; active-icon fill + color crossfade 0.15s
- **Presence change**: dot color crossfades 0.2s
- **Typing**: "Lena is typing…" text in the header subtitle
- **Haptics**: light impact on send, soft on reaction dock, success on call connect, warning on call end

## 7. Do's and Don'ts

### Do
- Make Skype Blue the brand — bright `#00AFF0` on buttons/FABs/tabs, deep `#0078D4` on the outgoing bubble
- Use white (`#FFFFFF`) light canvas / neutral near-black (`#16161A`) dark — neutral so blue stays vivid
- Render outgoing bubbles deep blue `#0078D4`, incoming gray (`#EBEBEF` / `#2A2A30`), ~14pt radius with a ~4pt tail
- Keep calling one tap away — phone + video in every chat header, dedicated Calls + Contacts tabs
- Interleave call history as inline call cards (glyph + duration + participants) in the chat
- Show presence dots on every avatar — green active `#2DC26B`, yellow away `#FFC400`, gray offline
- Use a gradient-tiled video grid with self-view, per-tile mic, and a big circular control bar
- Use green `#2DC26B` for accept/online, red `#E8364F` for end-call/destructive/unread badges
- Use deep-cyan ink `#00343F` for text on the bright `#00AFF0` button (AA); white text on the deep-blue bubble
- Use a clean humanist sans (Segoe UI → Inter/Open Sans), heavy 700/800 titles & buttons

### Don't
- Don't tint the dark canvas — Skype's dark mode is a neutral near-black `#16161A`, not color-cast
- Don't tint incoming bubbles blue — they're neutral gray; only outgoing is brand blue
- Don't use the bright `#00AFF0` for the outgoing bubble — use the text-safe deep `#0078D4`
- Don't put white text on the bright `#00AFF0` button — use deep-cyan ink `#00343F` for contrast
- Don't bury calling behind menus — it's call-first; header + Calls/Contacts tabs + call cards are mandatory
- Don't hide call history — every call appears as an inline card in the conversation
- Don't introduce a second brand accent — blue is it; green/yellow/red are presence/semantic only
- Don't use thin type for titles/buttons — Skype's friendliness comes from heavy 700/800 weights
- Don't use hard drop shadows on bubbles/call cards — they're flat; only floating menus elevate
- Don't drop presence dots — people-status is core to Skype's identity
- Don't animate aggressively — motion is soft spring, 0.15–0.32s

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max ~300pt; video grid caps at 4 tiles before paging |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above header / call top bar |
| iPhone 15/16 Pro Max | 430pt | Wider bubbles (~344pt), larger video tiles |
| iPad (portrait) | 768pt | Split: list (~340pt) + conversation/call; content max ~700pt centered |
| iPad (landscape) | 1024pt+ | Persistent list + call grid full-pane; gallery video layout |

### Dynamic Type
- Titles, headline, body, list preview, call-card title: full scale
- Timestamps, tab labels, badges, presence text, per-tile mic/name labels: FIXED
- Video tiles: fixed grid metrics (do not scale with text)

### Orientation
- Conversation supports rotation; bubbles reflow keeping the ~80% cap
- Video call rotates to landscape (gallery layout) with PiP support
- iPad keeps split view in both orientations; call grid uses the full pane

### Touch Targets
- Send / `+` / emoji buttons: 22–36pt glyph, 44pt hit
- Bubble: full bubble is the long-press (reactions) target
- Reaction glyphs: 28pt glyph, 44pt hit
- Header call icons: 44pt hit each
- Call-control buttons: 52–56pt (already ≥44pt)
- List/contact row, tab: full-row / full-cell tap

### Safe Area Handling
- Top: header / call top bar respects safe area + Dynamic Island
- Bottom: input bar + tab bar + call control bar respect home indicator; thread scrolls above keyboard
- Keyboard: compose bar and emoji panel ride above the keyboard
- Sides: 14pt bubble gutters; video tiles use the full width with 8pt gutters

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#16161A` (neutral near-black)
- Brand blue (bright): `#00AFF0` (buttons/FABs/tabs)
- Brand blue (deep): `#0078D4` (outgoing bubble, text-safe)
- Incoming bubble: `#EBEBEF` light / `#2A2A30` dark
- Bubble text (outgoing): `#FFFFFF`
- Text on bright `#00AFF0` button: `#00343F`
- Text primary: `#1B1B1F` light / `#F2F2F4` dark
- Text secondary: `#6E6E78` light / `#A4A4AE` dark
- Accept / online: `#2DC26B`
- End-call / unread badge: `#E8364F`
- Away presence: `#FFC400`
- Link: `#0078D4` / `#4FBDF0`

### Example Component Prompts
- "Build a Skype outgoing bubble in SwiftUI: background `#0078D4` (deep, text-safe Skype Blue), text `#FFFFFF` Segoe-substitute (Inter) 15pt regular, padding 9pt vertical / 14pt horizontal, corner radius 14pt with `.bottomTrailing` at 4pt (tail) when last in a same-sender run. Right-aligned, max width 80%. Footer: timestamp 10pt + delivery state, right-aligned. Incoming variant: background `#EBEBEF` (light) / `#2A2A30` (dark), text `label`, tail on `.bottomLeading`."

- "Create a Skype inline call card: a rounded surface interleaved in the chat. Background `#F4F4F6` (light) / `#2A2A30` (dark), 14pt corner radius, 10/14pt padding. Leading 30pt circular icon with a green phone glyph `#2DC26B` on an 18%-green fill (answered) or red `#E8364F` (missed). Title 'Group video call' Inter 13pt w700; subtitle '12 min · 3 participants' 11pt `secondaryLabel`. Optional trailing 'Call back' pill."

- "Render the Skype video call grid: an adaptive grid of participant tiles, each 14pt corner radius with a cloud-blue gradient fallback (`#34C3FF → #0078D4`) or live video. Name label bottom-left (11pt w600 white on a scrim). Per-tile mic indicator top-right: 18pt circle, `rgba(0,0,0,0.45)` bg, white mic glyph. A smaller bordered (`#34343C`) self-view tile pinned in the corner. Bottom control bar: a row of 56pt circular buttons — mute, video, share, more, and a red `#E8364F` end-call — on a translucent dark scrim."

- "Build the Skype primary button: pill, 22pt corner radius, background bright Skype Blue `#00AFF0`, label in deep-cyan ink `#00343F` (WCAG AA on bright blue) Inter 16pt w700, padding 13/26pt; pressed `#0091CC` + scale 0.98. Accept-call variant: background green `#2DC26B`, white icon+label. End-call: background red `#E8364F`, white phone-down glyph, 56pt circle in the call control bar."

- "Create the Skype chat header (call-first): 56pt + safe area, hairline divider. Leading `chevron.left` 17pt `#00AFF0`. Center-left: 36pt circular avatar with a presence dot (green `#2DC26B` active) + name Inter 15pt w700 stacked over status 'Active now' 11pt `#2DC26B`. Trailing: phone icon + video icon 22pt `#00AFF0`. Tap the header → contact profile (Chat / Audio / Video)."

- "Render the Skype bottom tab bar: 50pt + safe area, background `#FFFFFF`/`#1F1F24`, hairline top divider. Tabs: Chats, Calls, Contacts, Notifications. Icon 22pt; active = bright `#00AFF0` filled glyph + label; inactive = `#9A9AA4`/`#6F6F79`. Labels Inter 11pt w600 always shown; red `#E8364F` unread badge on Chats/Notifications. No tint pill — just icon fill + color change."

### Iteration Guide
1. Skype Blue is the brand — bright `#00AFF0` on buttons/FABs/tabs, deep text-safe `#0078D4` on the outgoing bubble
2. Dark canvas is a neutral near-black `#16161A` — NOT color-cast; neutral keeps the cloud blue vivid
3. Incoming bubbles are neutral gray `#EBEBEF` / `#2A2A30`; only the outgoing bubble is brand blue
4. Skype is call-first: phone + video in every chat header, dedicated Calls + Contacts tabs, the video grid
5. The chat is also a call log — interleave inline call cards (glyph + duration + participants) with messages
6. Presence dots (green active, yellow away, red DND, gray offline) appear on every avatar — people-status everywhere
7. The video grid (gradient tiles, self-view, per-tile mic, big circular control bar) is Skype's beating heart
8. Use deep-cyan ink `#00343F` on the bright `#00AFF0` button; white text on the deep `#0078D4` bubble
9. Green `#2DC26B` = accept/online; red `#E8364F` = end-call/destructive/unread; yellow `#FFC400` = away
10. Typography is a clean humanist sans (Segoe UI → Inter/Open Sans) with heavy 700/800 titles & buttons; bubbles flat ~14pt with a ~4pt tail
