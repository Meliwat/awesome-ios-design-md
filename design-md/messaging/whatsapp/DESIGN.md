# Design System Inspiration of WhatsApp (iOS)

## 1. Visual Theme & Atmosphere

WhatsApp's iOS app is a utility-first messenger dressed in a narrow, signature palette of green, teal, and white. The interface is aggressively native: tabbed bottom navigation, stock SF Pro typography, SF Symbols throughout, iOS-native swipe gestures, and native share sheets. WhatsApp has never tried to be a "platform" in the Instagram sense — it has tried to be the phone's SMS replacement, and the UI reflects that goal with near-monastic restraint. The app disappears, the message bubble is the hero.

The signature color moment is a two-stop green system: WhatsApp Green (`#25D366`) on the send button, floating action button, active read-receipt tick, and the voice-message record ring; and WhatsApp Teal (`#075E54`) as the legacy brand accent seen on link tints, the QR code screen, and the Meta-era "WhatsApp" brand chrome. Outgoing chat bubbles sit on a specific mint-leaf tint (`#D9FDD3` light mode, `#005C4B` dark mode), a color so load-bearing that it is immediately recognizable even as a single decontextualized pixel. Incoming bubbles are a neutral white/gray that fades into the wallpaper. The chat canvas itself carries a subtle doodle wallpaper by default — an optional cream pattern in light mode and a near-black dark-mode variant — that makes a busy chat feel warm rather than utilitarian.

Typography is pure SF Pro. WhatsApp ships no proprietary font; instead it leans on iOS Dynamic Type so aggressively that every label, timestamp, and bubble text scales up to accessibility sizes without breaking layout. Contact names in the chat list are 17pt semibold, message previews are 15pt regular in secondary gray, bubble text is 17pt regular, timestamps are 12pt regular. The typographic restraint is deliberate: this is a messenger built for a billion daily users across every literacy level and device generation, so the type system defers to whatever the OS wants to render.

**Key Characteristics:**
- WhatsApp Green (`#25D366`) reserved for send / voice-record / positive status — the verb color
- Teal (`#075E54`) as the legacy brand tint — QR code, link highlights, Meta-era chrome
- Mint-leaf outgoing chat bubble (`#D9FDD3` light / `#005C4B` dark) — iconic enough to pixel-identify
- Tabbed bottom navigation (2024+): Updates / Calls / Communities / Chats / Settings
- Voice message waveform as a signature interactive component with playback-synced bar fill
- Blue double-check read receipts — the universal "seen" signal
- End-to-end encryption lock symbol on every chat header — trust as UI
- Native SF Pro + SF Symbols everywhere — WhatsApp defers to iOS conventions
- Light / Dark / Auto themes; wallpaper customization per chat
- Status ring (green) around contact avatars with active Status updates

## 2. Color Palette & Roles

### Primary
- **WhatsApp Green** (`#25D366`): The send color — send button, FAB, active mic record ring, read-receipt "sent" green (before double-blue), phone-call answer button.
- **Green Pressed** (`#1EBE5D`): Pressed / active state for green CTAs.
- **Teal** (`#075E54`): Legacy brand accent — QR screen, business-account badge, link highlights in some older screens.
- **Mid Teal** (`#128C7E`): Secondary brand — used for outgoing bubble text accent, checkmark ticks pre-read.
- **Dark Teal** (`#054D44`): Deep brand chrome — rare, seen on splash and some dark-mode backgrounds.

### Chat Bubbles
- **Outgoing Bubble (Light)** (`#D9FDD3`): The mint-leaf tint on your own messages.
- **Outgoing Bubble (Dark)** (`#005C4B`): Dark-mode outgoing — deep teal-green.
- **Incoming Bubble (Light)** (`#FFFFFF`): Pure white on the wallpaper canvas.
- **Incoming Bubble (Dark)** (`#1F2C34`): A muted blue-gray that reads as "paper" in dark mode.
- **Reply Strip** (`#06CF9C` or `#25D366` 30% tint): The vertical accent bar on a quoted-reply preview inside a bubble.

### Canvas & Surfaces
- **Chat Wallpaper (Light)** (`#ECE5DD`): The iconic cream doodle-wallpaper background — modifiable per chat.
- **Chat Wallpaper (Dark)** (`#0B141A`): Default dark wallpaper — a deep blue-black.
- **App Canvas (Light)** (`#FFFFFF`): Chat list and settings screens.
- **App Canvas (Dark)** (`#111B21`): Primary dark canvas — a slight blue tint, not true black.
- **Surface 1 (Light)** (`#F7F8FA`): Section group background on Settings.
- **Surface 1 (Dark)** (`#202C33`): Elevated surface — input bar background, reply strip.
- **Surface 2 (Dark)** (`#2A3942`): Higher elevation — system messages, pinned bar, context menus.
- **Divider (Light)** (`#E9EDEF`): Hairline between chat list rows.
- **Divider (Dark)** (`#222D34`): Dark-mode hairline.

### Text
- **Text Primary (Light)** (`#111B21`): Bubble text, contact names, screen titles.
- **Text Secondary (Light)** (`#667781`): Message preview, timestamps, section subtitles.
- **Text Tertiary (Light)** (`#8696A0`): Placeholder, disabled labels.
- **Text Primary (Dark)** (`#E9EDEF`): Bubble text and titles in dark mode.
- **Text Secondary (Dark)** (`#8696A0`): Message preview in dark mode.
- **Timestamp in-Bubble** (`#667781` light / `#8696A0` dark): The time stamp inside an outgoing or incoming bubble.

### Semantic
- **Read Blue** (`#53BDEB`): The double blue ticks — read receipt. This is THE most semantically loaded color in the app.
- **Sent Gray** (`#8696A0`): Single gray tick — sent but not delivered.
- **Delivered Gray** (`#8696A0`): Double gray ticks — delivered but not read.
- **Error Red** (`#F15C6D`): Failed-to-send bubble tint, delete chat confirmation.
- **Record Red** (`#F15C6D`): Voice-record active ring (rare — WhatsApp uses green here, red is reserved for in-recording decibel meter overflow).
- **Status Ring Green** (`#25D366`): The ring around avatars with active, unread Status updates.
- **Status Ring Gray** (`#8696A0`): Status ring for viewed statuses.
- **Verified Green Check** (`#25D366`): Official business badge checkmark.

### Dark Mode
WhatsApp's dark mode is a blue-black utility — it's not true black (that would bleach the mint-leaf bubble) and it's not quite gray (that would lose the WhatsApp feel). It's `#0B141A` on the chat wallpaper and `#111B21` on the app canvas.
- All accent colors retain their light-mode hex; brand greens stay bright.
- The outgoing bubble shifts from mint-leaf to deep teal-green (`#005C4B`) for contrast.
- Input bar background becomes `#202C33` to sit above the wallpaper.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` (body) and `SF Pro Display` (titles) — Apple's stock system fonts. WhatsApp ships no proprietary typeface.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace (rare)**: `SF Mono` — used only in formatted code blocks inside bubbles (triple-backtick Markdown formatting).
- **Non-Latin**: Full Dynamic Type support across all iOS-supported scripts. WhatsApp relies entirely on OS font substitution.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title (Chats) | SF Pro Display | 34pt | 700 | 1.12 | 0.37pt | "Chats" scroll-collapsing large title |
| Screen Title | SF Pro Display | 17pt | 600 | 1.18 | -0.4pt | Nav bar title when collapsed |
| Contact Name (Chat List) | SF Pro Text | 17pt | 600 | 1.23 | -0.4pt | Contact in chat list row |
| Message Preview | SF Pro Text | 15pt | 400 | 1.33 | -0.2pt | Last message preview below name |
| Bubble Body | SF Pro Text | 17pt | 400 | 1.29 | -0.4pt | The message text inside a chat bubble |
| Bubble Sender (Group) | SF Pro Text | 13pt | 600 | 1.23 | -0.1pt | Sender name in group chat, colored |
| Timestamp (List) | SF Pro Text | 12pt | 400 | 1.2 | 0pt | Relative time on chat-list rows |
| Timestamp (Bubble) | SF Pro Text | 11pt | 400 | 1.0 | 0pt | In-bubble time in secondary gray |
| Section Header | SF Pro Text | 13pt | 600 | 1.2 | -0.1pt | "PINNED", "ARCHIVED" — uppercase tracked |
| System Message | SF Pro Text | 12pt | 500 | 1.3 | 0pt | "Messages are end-to-end encrypted" center pill |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button (Primary) | SF Pro Text | 17pt | 600 | 1.0 | -0.4pt | "Continue", "Next" — native iOS button type |
| Input Placeholder | SF Pro Text | 16pt | 400 | 1.3 | -0.3pt | "Message" placeholder in compose bar |
| Settings Row | SF Pro Text | 17pt | 400 | 1.23 | -0.4pt | Settings row title |
| Settings Detail | SF Pro Text | 15pt | 400 | 1.3 | -0.2pt | Value on right side of settings row |

### Principles
- **Defer to iOS**: WhatsApp follows Apple Human Interface Guidelines type sizes almost exactly — large titles, native body, footnote.
- **Dynamic Type first**: Every body/preview/bubble text scales with user preference. Layout wraps gracefully up to Accessibility XL.
- **Weight used sparingly**: 400 for body, 600 for names/titles, 700 only on large titles. No 800, no 300.
- **Color contrast matters more than size**: Message preview (15pt gray) and contact name (17pt black) rely on color weight differentiation, not typographic scale.
- **Sender color in groups**: In group chats each sender's name is tinted in one of ~20 pre-assigned colors to aid scanning.
- **Timestamps shrink to fit**: When an outgoing bubble has short text, the timestamp floats inside the last line; for long messages it drops below.

## 4. Component Stylings

### Buttons

**Primary Green Send Button (The Circular FAB)**
The single iconic action in WhatsApp. A circular button on the compose bar that morphs: microphone icon when the input is empty, paper-plane when text is entered.
- Shape: Circle, 36pt diameter (in compose bar) or 52pt (FAB on Chats tab "new message")
- Background: `#25D366`
- Icon: SF Symbol `paperplane.fill` or `mic.fill`, 18-22pt, white
- Pressed: scale 0.92 + soft haptic, background shifts to `#1EBE5D`
- Voice-record mode: hold the button; ring expands out and fills with decibel-responsive color; drag up to lock recording

**Primary Pill CTA ("Allow", "Continue")**
- Background: `#25D366`
- Text: white, SF Pro Text 17pt weight 600
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 24pt (pill) for onboarding, 12pt for compact CTAs
- Pressed: `#1EBE5D`, scale 0.97

**Secondary Text Button ("Skip", "Not Now")**
- Background: transparent
- Text: `#25D366` (green) for affirmative alternates, `#F15C6D` for destructive
- Font: SF Pro Text 17pt weight 400
- No border, no fill — native iOS link style

**Icon Actions (Attach, Camera, Emoji, Mic)**
- Size: 22-24pt glyph, 44pt hit area
- Default: `#8696A0`
- Active: `#25D366` (green, when active)

**Call Answer / Decline Button (Voice/Video Call Screen)**
- Answer: 72pt circle `#25D366`, white `phone.fill` icon, subtle pulsing green outer ring
- Decline: 72pt circle `#F15C6D`, white `phone.down.fill` icon

### Cards & Containers

**Chat List Row**
- Height: 72pt
- Structure: 48pt circular avatar (leading, 16pt from edge) → 12pt gap → stacked (name 17pt w600 + preview 15pt w400 `#667781`) → trailing (timestamp 12pt w400 right + unread badge below)
- Background: `#FFFFFF` (light) / `#111B21` (dark)
- Pressed: background `#F0F2F5` (light) / `#1F2C34` (dark)
- Swipe-left reveals: Archive (gray), Mute (amber), Delete (red) — native iOS swipe actions
- Swipe-right reveals: Read / Unread toggle (blue)
- Pinned row: subtle pin icon trailing, small `#25D366` stroke indicator

**Chat Bubble (Outgoing)**
- Max width: 80% of chat canvas
- Background: `#D9FDD3` light / `#005C4B` dark
- Text color: `#111B21` light / `#E9EDEF` dark
- Padding: 8pt vertical, 12pt horizontal
- Corner radius: 12pt all corners, **except** the bottom-trailing corner which is 0pt for the "tail" nub (or a pointed SVG tail hanging below-right)
- Trailing timestamp + tick row: 11pt `#667781`/`#8696A0` inline at bottom-right, check icon 12pt
- Tail: small triangle `#D9FDD3`/`#005C4B` at bottom-right of bubble (only on the last bubble from sender in a grouped sequence)

**Chat Bubble (Incoming)**
- Max width: 80%
- Background: `#FFFFFF` light / `#1F2C34` dark
- Text color: `#111B21` light / `#E9EDEF` dark
- Padding: 8pt vertical, 12pt horizontal
- Corner radius: 12pt all corners, **except** bottom-leading corner 0pt for tail
- Sender name (group only): top line, 13pt weight 600, tinted sender color

**Reply-Quote Bubble (Inside a Message)**
- Nested inside an outgoing or incoming bubble at the top
- 4pt leading vertical accent bar in sender's group color (or green for self)
- Background: 0.08 opacity over parent bubble
- Text: 13pt regular, truncated to 2 lines

**Settings Group (iOS Grouped List)**
- Background: `#F7F8FA` light / `#111B21` dark
- Row fill: `#FFFFFF` light / `#1F2C34` dark
- Corner radius: 10pt on first and last row of group, 0pt in middle
- Divider: 0.5pt `#E9EDEF` indented to text

### Navigation

**Bottom Tab Bar (iOS 2024+)**
- Height: 49pt + safe area
- Background: `.regularMaterial` blur over `#FFFFFF` (light) / `#111B21` (dark) at 92% opacity
- Tabs: Updates, Calls, Communities, Chats, Settings
- Icon size: 24pt
- Active color: `#25D366` (green — unlike iOS Messages, WhatsApp uses brand green as active tint)
- Inactive: `#8696A0`
- Labels: SF Pro Text 10pt weight 500, always shown
- Badge: red circle `#F15C6D` with white count, top-right of icon

**Top Nav (Chat Screen)**
- Height: 44pt + safe area
- Leading: back chevron 17pt + avatar (32pt circular) + contact name + typing/last-seen status
- Trailing: video-call icon (22pt) + voice-call icon (22pt) + ellipsis menu
- Background: `#F0F2F5` light / `#202C33` dark (a subtle surface tint)
- Typing indicator: 13pt green "typing..." below name

**Top Nav (Chat List / Updates)**
- Large title "Chats" scroll-collapses to standard nav
- Leading: "Edit" button (blue text)
- Trailing: New Chat icon (green `#25D366`, square.and.pencil SF Symbol)
- Search bar below title: pill shape, `#F0F2F5` background, 15pt

### Input Fields

**Compose Bar (Chat Input)**
- Height: ~52pt when single-line, grows up to ~120pt for multiline
- Structure: leading emoji/sticker icon (24pt `#8696A0`) → multiline text field → trailing (attach paperclip 24pt + camera 24pt + mic/send circle 36pt green)
- Background: `#FFFFFF` light / `#202C33` dark (elevated surface)
- Input field inset: 12pt padding, 20pt corner radius pill
- Placeholder: "Message", 16pt weight 400 `#8696A0`
- Focus: no border change, keyboard slides up, "Send" circle morphs from mic to paperplane

**Search Bar**
- Background: `#F0F2F5` light / `#202C33` dark
- Corner radius: 10pt
- Height: 36pt
- Leading magnifyingglass `#8696A0`
- Placeholder: "Search" `#8696A0`
- Focus: tiny cancel button slides in trailing

### Distinctive Components

**Voice Message Waveform Bubble**
The signature WhatsApp interactive component. A voice message rendered as a horizontal waveform inside the bubble.
- Height: ~52pt bubble total
- Leading: play/pause circle (36pt, `#25D366` background, white `play.fill` / `pause.fill`)
- Center: waveform — ~40-60 vertical bars, each 2pt wide with 2pt spacing
- Bar color: played bars = `#25D366` (or `#128C7E` in teal-heavy legacy), unplayed bars = `#8696A0`
- Bar heights: variable per actual audio amplitude (2-20pt range)
- Trailing: duration "0:23" 11pt `#8696A0`, below that a speed pill "1x" / "1.5x" / "2x" when playing
- Avatar dot (tiny): for group voice messages, the sender's avatar sits just above the waveform
- Haptics: soft tick when playback reaches each "amplitude peak" bar (subtle)

**Status Ring (Around Avatar)**
- Diameter: avatar + 6pt outer ring
- Ring width: 2.5pt
- Ring color: `#25D366` if unviewed status updates exist; `#8696A0` if all viewed
- Segmentation: if multiple status updates, ring breaks into N arc segments with 4-degree gaps
- Animation on tap: ring fills progress as viewer scrubs through statuses

**Read Receipts (Tick Row)**
- Single gray tick (`#8696A0`) = sent to server
- Double gray tick = delivered to device
- Double blue tick (`#53BDEB`) = read
- Position: inline with timestamp at bottom-right of outgoing bubble
- Size: 14pt check marks, tightly kerned so they overlap slightly
- Animation: blue ticks animate in with a slight fade when the other party opens the message

**End-to-End Encryption Lock Banner**
- Center pill in chat canvas, 1pt shadow, `#FFF5C4` (light) / `#1F2C34` (dark) background
- SF Symbol `lock.fill` 12pt + "Messages are end-to-end encrypted. No one outside this chat..."
- 12pt text weight 500, `#54636D`
- Appears as first message in every new chat

**View-Once Media Indicator**
- Circular "1" badge in the corner of media preview in chat bubble
- After viewing: bubble collapses to "You opened" or "Opened" text with ghost-eye SF Symbol

**Emoji Reaction Palette**
- 6 default emojis: heart, thumbs-up, laugh, surprise, cry, pray — plus "+" for custom
- Palette: horizontal floating capsule above the message on long-press
- Background: `#FFFFFF` light / `#2A3942` dark with soft shadow
- Emoji size: 32pt, spacing 8pt
- On selection: emoji flies down onto bottom-right of bubble as a small 14pt chip
- Haptic: soft impact on selection

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56, 72
- Standard screen margin: 16pt horizontal
- Chat bubble margin from screen edge: 8pt (tighter than app canvas)
- Between chat bubbles (same sender): 2pt; different sender: 8pt

### Grid & Container
- Content width: full device width
- Chat canvas width: full bleed; bubbles constrained to 80% max width
- Settings list: full width with 16pt horizontal padding inside rows

### Whitespace Philosophy
- **Density tuned for browsing**: Chat list rows at 72pt are comfortable; bubble padding at 8-12pt is tight enough for a rapid-fire conversation.
- **Wallpaper as negative space**: The cream/dark doodle wallpaper is visible between bubbles — it's not empty, it's atmospheric.
- **No decorative padding**: Every pixel does a job. Avatars are 40-48pt, buttons are sized to their content.

### Border Radius Scale
- Square (0pt): Status segment arcs (rare), media thumbnails can be 0pt in grid
- Soft (4pt): Media previews inside bubble
- Standard (10pt): Search bar, section group cards, settings row corners
- Bubble (12pt): Chat bubbles — 12pt on all corners except the tail corner (0pt)
- Comfortable (16pt): Onboarding CTAs, bottom sheet grabber handles
- Pill (20-24pt): Compose input field, filter chips
- Full Circle (50%): Avatars, mic button, send button, status ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow | Chat list rows, settings rows, bubbles on wallpaper |
| Bubble (1) | `rgba(0,0,0,0.08) 0 1px 2px` | Subtle shadow under chat bubbles, extremely soft |
| Card (2) | `rgba(0,0,0,0.12) 0 2px 8px` | Reaction palette floating menu |
| Sheet (3) | `rgba(0,0,0,0.2) 0 -12px 32px` | Bottom action sheet, attachment tray |
| Modal Overlay | `rgba(0,0,0,0.4)` backdrop | Long-press overlay, dimming behind reactions sheet |
| Nav Blur | `.regularMaterial` 92% opacity | Top nav bar and bottom tab bar |

**Shadow Philosophy**: Shadows are extremely restrained. The bubble itself has at most a 1-2pt shadow to separate it from the wallpaper, and only reaction/attachment popovers lift noticeably. The app feels flat, mirroring iOS's own aesthetic.

### Motion
- **Send button press**: Scale 0.92 → 1.0 spring + soft haptic
- **Mic record hold**: Ring pulses outward 1.0 → 1.2 with decibel-responsive scale
- **Message send**: Bubble slides up from compose bar, ticks animate in after 300ms latency
- **Read receipt update**: Ticks cross-fade from gray to blue over 200ms
- **Swipe-to-reply**: Native iOS-style 60fps horizontal drag, haptic tick at threshold
- **Reaction tap**: Emoji scales 1.0 → 1.2 → 1.0 with soft haptic
- **Voice playback**: Waveform bars progressively fill green left-to-right synced to audio
- **Status ring fill**: Circular progress animates during status viewing (linear)

## 7. Do's and Don'ts

### Do
- Use `#25D366` (WhatsApp Green) for send, answer-call, record, and positive primary actions
- Use `#D9FDD3` (light) or `#005C4B` (dark) for outgoing bubbles — the mint-leaf is iconic
- Keep bubble tails asymmetric — rounded all corners except one pointed at sender
- Rely on Dynamic Type; never hardcode pt sizes that won't scale
- Use SF Pro + SF Symbols as your default — WhatsApp looks native because it IS native
- Use blue double ticks (`#53BDEB`) for read receipts — this is universally understood
- Use the lock icon for end-to-end encryption indicators — the semantic weight matters
- Show the encryption banner as the first message in every new chat
- Blur the top nav and bottom tab bar with `.regularMaterial`
- Use green status rings for unread, gray for viewed status updates

### Don't
- Don't use pure black canvas — WhatsApp dark mode is `#111B21`, a blue-black
- Don't round bubble corners equally — the asymmetric tail is core to the form language
- Don't use red for send or positive actions — red is reserved for delete, fail, and call-decline
- Don't place the "new chat" FAB inside the tab bar — keep it as a nav-bar trailing icon
- Don't add custom fonts — WhatsApp's iOS typography is pure SF Pro
- Don't use gradients on UI chrome — WhatsApp is flat color
- Don't hide the timestamp inside bubbles at small sizes — users scan bubbles for recency
- Don't use green text — green is structural (send, answer, read, status ring), not decorative
- Don't break out of native iOS swipe-actions for row gestures
- Don't use heavy shadows — WhatsApp is aggressively flat

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max-width shrinks to 78%; waveform loses ~5 bars |
| iPhone 13/14/15 | 390pt | Standard bubble 80% max-width |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in calls nav |
| iPhone 15/16 Pro Max | 430pt | Bubble 80%; avatar grows to 52pt in chat list |
| iPad | 768pt+ | Split view: chat list left, conversation right |

### Dynamic Type
- Bubble body, chat-list name and preview: full scale up to Accessibility XL
- Timestamps (11-12pt): fixed size to preserve inline layout
- Tab labels: fixed 10pt
- Bubble auto-wraps at any scale; bubble max-width shrinks with XL sizes

### Orientation
- Chat list / Updates / Settings: **portrait-locked** on iPhone
- Video calls: **rotate to landscape**
- Media viewer (photos / videos inside chat): **rotate to landscape**
- iPad: all orientations supported; split view adapts to landscape width

### Touch Targets
- Send button: 36pt in compose bar (native iOS minimum-plus hit target)
- Mic button: same 36pt circle, 44pt effective hit area with hitSlop
- Chat list row: 72pt height, full-row tappable
- Swipe actions: native iOS swipe width, 80pt per action
- Reaction emojis in palette: 32pt glyph, 44pt effective

### Safe Area Handling
- Top: safe area honored; call screen extends into Dynamic Island with pill treatment
- Bottom: compose bar floats above safe area; keyboard pushes content up natively
- Sides: 16pt on app canvas, 8pt on chat canvas for bubble alignment

## 9. Agent Prompt Guide

### Quick Color Reference
- WhatsApp Green: `#25D366`
- Green Pressed: `#1EBE5D`
- Teal (legacy): `#075E54`
- Mid Teal: `#128C7E`
- Outgoing bubble (light): `#D9FDD3`
- Outgoing bubble (dark): `#005C4B`
- Incoming bubble (light): `#FFFFFF`
- Incoming bubble (dark): `#1F2C34`
- Chat wallpaper (light): `#ECE5DD`
- Chat wallpaper (dark): `#0B141A`
- App canvas (light): `#FFFFFF`
- App canvas (dark): `#111B21`
- Text primary (light): `#111B21`
- Text secondary (light): `#667781`
- Text primary (dark): `#E9EDEF`
- Text secondary (dark): `#8696A0`
- Read-receipt blue: `#53BDEB`
- Error / decline red: `#F15C6D`

### Example Component Prompts
- "Create a SwiftUI WhatsApp chat bubble (outgoing): max-width 80% of parent, 12pt padding, background #D9FDD3 in light / #005C4B in dark, 12pt corner radius on all corners except bottom-trailing (0pt for tail). Text in SF Pro 17pt weight 400 #111B21. Inline trailing time '10:24' in 11pt #667781 followed by double blue check ticks #53BDEB, 12pt SF Symbol 'checkmark'."
- "Build the WhatsApp send button: 36pt circular Pressable, background #25D366, SF Symbol 'paperplane.fill' 18pt white center. When text input is empty, morph to 'mic.fill'. Pressed state: scale 0.92 + soft haptic, background #1EBE5D. iOS 17+ .sensoryFeedback on trigger."
- "Design the chat list row: 72pt height, leading 48pt circular avatar with optional 2.5pt green ring (#25D366) for unread status, 12pt gap, stacked contact name in SF Pro 17pt weight 600 and message preview in 15pt weight 400 #667781, trailing 12pt timestamp top-right and optional #25D366 unread badge below. Swipe-left reveals Archive (#8696A0), Mute (#FF9500), Delete (#F15C6D)."
- "Build the voice-message waveform bubble: ~52pt tall bubble with mint-leaf (#D9FDD3) background, 12pt corner radius (asymmetric tail). Leading 36pt #25D366 play circle with white play.fill, horizontal waveform of 50 bars (2pt wide, 2pt gap) with variable 2-20pt heights. Played bars #25D366, unplayed #8696A0. Trailing duration '0:23' in 11pt #8696A0 and a '1x' speed pill."
- "Create the compose bar: pill-shaped multiline input with 20pt radius, background #FFFFFF on a #F0F2F5 light canvas (or #202C33 on dark), placeholder 'Message' 16pt #8696A0. Leading emoji SF Symbol 'face.smiling' 24pt #8696A0. Trailing camera SF Symbol 'camera.fill' 24pt and the 36pt #25D366 send/mic morph button."
- "Design the WhatsApp bottom tab bar: Updates, Calls, Communities, Chats, Settings — 24pt SF Symbol icons (bubble.left.and.bubble.right.fill for Communities, message.fill for Chats). Active tint #25D366, inactive #8696A0. Labels SF Pro 10pt weight 500. Blur background .regularMaterial over #FFFFFF / #111B21 at 92% opacity."

### Iteration Guide
1. Canvas is `#FFFFFF` (light) / `#111B21` (dark) — the dark is a blue-black, NOT pure black
2. Chat wallpaper is SEPARATE from app canvas — `#ECE5DD` cream (light) / `#0B141A` deep (dark)
3. Outgoing bubbles are `#D9FDD3` / `#005C4B` — this is THE signature pixel of WhatsApp
4. Bubble corner radius is 12pt on all corners EXCEPT the tail (0pt on bottom-trailing for outgoing, bottom-leading for incoming)
5. WhatsApp Green `#25D366` only for send / answer-call / voice-record / status-ring / read-tick green — never decorative
6. Blue double check (`#53BDEB`) is a LOAD-BEARING color — do not repurpose
7. Typography is pure SF Pro — NO custom fonts; weights 400, 500, 600, 700 only
8. Dynamic Type is respected everywhere except inline timestamps (11-12pt fixed)
9. Read receipts use tiny 12-14pt overlapping check marks, not separate dots
10. End-to-end encryption banner is THE first message in every new chat — don't skip it
