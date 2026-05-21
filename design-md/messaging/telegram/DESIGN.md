# Design System Inspiration of Telegram (iOS)

## 1. Visual Theme & Atmosphere

Telegram's iOS app is a messenger that lets the user's taste dominate the chrome. Out of the box it ships a confident Sky Blue (`#0088CC`) accent over a true-white or pure-black canvas, but the entire color system is a "themeable surface" — users can pick any hex code as an accent, apply custom chat backgrounds, even import community themes with rainbow gradients. That theming flexibility is the brand. A Telegram install in 2026 can look like anything from a corporate blue client to a neon-purple night-theme maximalist setup, and all of it is *still* unmistakably Telegram because the UI rhythm — bubble shape, nav structure, type scale, gesture language — holds steady no matter what color is poured in.

The default light experience is a clean white canvas with Sky Blue (`#0088CC` / `#40A7E3`) accents on the send button, the active tab, and the link color. The default dark experience is a crushed near-black (`#212121` or even `#000000` on OLED mode) that maximizes battery life and readability. Where WhatsApp chose a warm cream wallpaper, Telegram chose *emptiness*: chat canvas is a flat color by default. The mood is Russian-tech-maximalist — lots of white space, lots of speed, lots of customization hooks; think Durov-aesthetic where every screen feels engineered rather than designed.

Typography is SF Pro on iOS, rendered with Telegram's very particular attention to size hierarchy. Chat bubbles default to 17pt regular; contact names 17pt semibold; timestamps 11pt. But Telegram's real typographic signature is how it renders *expressive content*: animated emoji (Lottie-level vector animations on message-sent triggers), custom sticker packs that auto-scale, syntax-highlighted code blocks in monospace, and message formatting (bold, italic, spoiler, quote, code) applied inline. A Telegram chat is a typographic playground in a way WhatsApp's intentionally is not.

**Key Characteristics:**
- Sky Blue (`#0088CC` / `#40A7E3`) default accent — but **every color is themeable**, including custom user-picked hex
- True white canvas (`#FFFFFF`) light / crushed black (`#212121` or `#000000` OLED) dark
- Outgoing bubble (`#2B86FD` blue, default theme) — bubble color is themeable; some themes use gradients
- Custom chat backgrounds per-chat (gradients, patterns, photos, solid colors)
- Swipe-from-edge to reply — full-screen iOS-native pan gesture, not a right-swipe on the row
- Floating mini-player for voice messages and music — pinned across chat navigation
- Animated emoji + sticker packs (Lottie-style vector animations) that play on send
- Silent message send (long-press the send button) — a signature Telegram gesture
- Secret chats with self-destruct timers — visually distinct with a lock icon on the chat header
- Channel vs Group distinction — broadcast-only channels have "Subscribers" (no reply affordance)
- Custom Telegram-style outlined icons — NOT SF Symbols everywhere, a proprietary icon vocabulary
- Up to 2GB file sharing, shown inline as a progress card
- OLED dark mode (`#000000`) as a toggle option separate from standard dark

## 2. Color Palette & Roles

### Primary (Default Blue Theme)
- **Telegram Blue** (`#0088CC`): Classic accent — nav bar tint, text links, active tab icon.
- **Sky Blue** (`#40A7E3`): Lighter accent — selection highlights, toggles ON.
- **Bubble Blue (Outgoing)** (`#2B86FD`): Outgoing chat bubble fill in the default blue theme.
- **Deep Blue (Pressed)** (`#0071B0`): Pressed state for primary blue buttons.

### Accent Variants (User-Selectable)
Telegram lets the user pick any accent color. The UI adapts — every blue surface re-paints to the chosen hex. Common accent palette Telegram offers:
- Green Mint (`#3FC1B0`), Rose (`#FF5C77`), Orange (`#FB8C4A`), Purple (`#6F7BF7`), Raspberry (`#C9426B`), Cyan (`#2AAFBA`), Yellow (`#FFAC45`).

### Canvas & Surfaces (Light Theme)
- **Canvas** (`#FFFFFF`): Chat list, settings, chat canvas when no wallpaper set.
- **Chat Background (Default Blue Wallpaper)** (`#DBE7F4`): Default soft-blue chat wallpaper (though user can set any).
- **Surface 1** (`#F7F7F7`): Section group background, settings list tint.
- **Surface 2** (`#EFEFF4`): iOS grouped list background.
- **Divider** (`#C7C7CC`): Hairline separator (iOS default tint).

### Canvas & Surfaces (Dark Theme — Default)
- **Canvas (Dark)** (`#212121`): Standard dark canvas — a crushed near-black.
- **Surface 1 (Dark)** (`#1C1C1C`): Card background, elevated surface.
- **Surface 2 (Dark)** (`#2C2C2E`): Bottom-sheet, modal, context-menu background.
- **Divider (Dark)** (`#383838`): Dark-mode hairline separator.

### Canvas & Surfaces (OLED Dark — Optional)
- **OLED Canvas** (`#000000`): Pure black for OLED battery savings.
- **OLED Surface** (`#0A0A0A`): Subtle elevation over pure black.

### Chat Bubbles
- **Outgoing Bubble (Light)** (`#2B86FD`): Default blue theme — bubble background.
- **Outgoing Bubble Gradient**: Some themes use `#2B86FD → #61B3FF` vertical gradient.
- **Outgoing Text** (`#FFFFFF`): White text on blue bubble.
- **Incoming Bubble (Light)** (`#FFFFFF`): White fill with 1pt `#E5E5E5` border.
- **Incoming Bubble (Dark)** (`#2A2A2A`): Dark-mode incoming fill.
- **Secret Chat Outgoing** (`#4EA53C` green tint): Secret chat messages are subtly different.

### Text
- **Text Primary (Light)** (`#000000`): Bubble text, contact names, titles.
- **Text Secondary (Light)** (`#707579`): Message preview, timestamps, metadata.
- **Text Tertiary (Light)** (`#A0A6AD`): Placeholder, disabled.
- **Text Primary (Dark)** (`#FFFFFF`): Dark-mode primary.
- **Text Secondary (Dark)** (`#8D8E93`): Dark-mode metadata.

### Semantic
- **Read Receipt Blue** (`#0088CC`): Double check = seen. Color matches accent.
- **Sent Gray** (`#B1B1B1`): Single check = sent.
- **Error Red** (`#E53935`): Destructive actions, failed send.
- **Destructive Red** (`#E35561`): Delete chat, block user, kick member.
- **Online Green** (`#4DD364`): "Online now" status under contact name.
- **Verified Blue** (`#0088CC` with check): Verified channels / users.
- **Premium Purple** (`#AE6FFD → #CE6BFF` gradient): Telegram Premium badge, stars, and special reactions.

### Sender Colors (Group Chats)
Telegram assigns each sender a color from a 7-color palette for their name in group chats — for scannability. Typical palette:
- `#FC5C51` (red), `#FA790F` (orange), `#895DD5` (purple), `#0FB297` (green), `#00C1A6` (teal), `#3CA5EC` (blue), `#FF5274` (pink).

### Message States
- **Unread Divider** (`#B1B1B1` text + `#E5E5E5` line or `#0088CC` accent): Horizontal "Unread messages" line dividing old and new messages in a chat.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` (body) and `SF Pro Display` (titles) — iOS native. Telegram ships no proprietary font in its mobile app.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace**: `SF Mono` — used in code blocks (triple-backtick Markdown), pre-formatted text.
- **Emoji**: Apple Color Emoji — Telegram also supports custom emoji packs that render as animated Lottie files inline.
- **Non-Latin**: Full iOS font substitution; Telegram is heavily used in Cyrillic, Arabic, Persian, CJK.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 34pt | 700 | 1.12 | 0.37pt | "Chats", "Settings" large titles |
| Screen Title | SF Pro Display | 17pt | 600 | 1.18 | -0.4pt | Nav bar title collapsed state |
| Contact Name | SF Pro Text | 17pt | 600 | 1.23 | -0.4pt | Chat list, profile titles |
| Bubble Body | SF Pro Text | 17pt | 400 | 1.29 | -0.4pt | Chat bubble text |
| Bubble Sender (Group) | SF Pro Text | 14pt | 600 | 1.23 | -0.2pt | Sender name inside bubble (tinted color) |
| Message Preview | SF Pro Text | 15pt | 400 | 1.33 | -0.2pt | Chat list last message preview |
| Timestamp (List) | SF Pro Text | 13pt | 400 | 1.2 | 0pt | Chat list time |
| Timestamp (Bubble) | SF Pro Text | 11pt | 400 | 1.0 | 0pt | In-bubble timestamp |
| Section Header | SF Pro Text | 13pt | 400 | 1.2 | 0pt | iOS grouped list section labels |
| Body | SF Pro Text | 17pt | 400 | 1.4 | -0.4pt | Body text, Settings rows |
| Callout | SF Pro Text | 16pt | 400 | 1.3 | -0.3pt | Link previews, attachments |
| Footnote | SF Pro Text | 13pt | 400 | 1.3 | -0.1pt | Sub-copy, hints |
| Caption | SF Pro Text | 12pt | 400 | 1.3 | 0pt | Metadata, file sizes |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button (Primary) | SF Pro Text | 17pt | 600 | 1.0 | -0.4pt | iOS native button type |
| Code (Monospace) | SF Mono | 15pt | 400 | 1.4 | 0pt | Inline code & code blocks |
| Blockquote | SF Pro Text | 17pt | 400 italic | 1.3 | -0.4pt | Quoted-reply block text |

### Principles
- **Defer to iOS**: Like WhatsApp, Telegram's iOS app adheres to Apple HIG type sizes closely.
- **Dynamic Type supported**: Bubble text, contact names, message previews all scale.
- **Inline formatting**: Telegram renders bold, italic, underline, strikethrough, spoiler, code, pre, link as inline styling inside bubbles using Markdown-ish syntax (bold via `**`, italic via `__`, spoiler via `||`).
- **Theme-coupled color**: Link color and sender-name tints inherit from the user's chosen accent color.
- **Animated emoji replace static**: When a recipient sends a single emoji, Telegram often plays a fullscreen Lottie animation — type escapes its frame.

## 4. Component Stylings

### Buttons

**Primary Send Button (In Compose Bar)**
- Shape: Tappable text/icon button, no container
- Icon: Custom Telegram paper-plane glyph (NOT SF Symbol) — 26pt
- Color: `#0088CC` (accent) when text present; same as inactive when empty (shows `mic` / `camera` icons instead)
- Long-press: Opens silent-send / schedule-send menu
- Haptic: soft impact on send

**Primary Button (Sheet / Onboarding CTA)**
- Shape: Full-width rectangle, 10pt corner radius
- Height: 50pt
- Background: `#0088CC` (accent)
- Text: white, SF Pro Text 17pt weight 600
- Pressed: `#0071B0` + scale 0.98
- Used for: "Continue", "Log In", "Create Account", "Join"

**Secondary Text Button**
- Background: transparent
- Text: `#0088CC` (accent) or `#E53935` (destructive)
- Font: SF Pro Text 17pt weight 400
- No fill, no border — pure iOS link style

**Floating Send in Compose (with morph)**
- When text is empty: show mic + attach + camera icons (24pt each, gray `#707579`)
- When text is present: send paper-plane icon (`#0088CC`) replaces the cluster
- Crossfade morph on keystroke change

**Floating Action Menu (Compose Attach)**
- Triggered by + attach icon
- Bottom sheet with labeled grid: Photo/Video, File, Location, Contact, Poll, Music
- Each tile: 64pt circular blue icon + 13pt label beneath

### Cards & Containers

**Chat List Row**
- Height: 76pt
- Structure: 54pt circular avatar (leading, 16pt inset) → 12pt gap → stacked (name + preview + verified check if applicable) → trailing (time + unread pill + muted icon + pinned icon)
- Background: `#FFFFFF` light / `#212121` dark
- Pressed: `#F2F2F7` light / `#2C2C2E` dark
- Swipe-left: Mute, Delete, More
- Swipe-right: Read/Unread, Pin, Archive
- Pinned row: slightly shaded background `#F7F7F7` and pin glyph trailing

**Chat Bubble (Outgoing — Default Blue Theme)**
- Max width: 80% of chat canvas
- Background: `#2B86FD` (or gradient `#2B86FD → #61B3FF` in gradient themes)
- Text color: white
- Padding: 8pt vertical, 12pt horizontal
- Corner radius: 17pt on all corners EXCEPT bottom-trailing which is 6pt (soft tail, not a full point)
- Trailing: 11pt white timestamp + double check read-tick (white)
- Tap-and-hold: reveals reactions + context menu (copy, reply, forward, edit, delete, pin)

**Chat Bubble (Incoming)**
- Max width: 80%
- Background: `#FFFFFF` light / `#2A2A2A` dark
- Text color: `#000000` light / `#FFFFFF` dark
- 1pt border: `#E5E5E5` light only (visible on plain-white wallpapers)
- Corner radius: 17pt all corners EXCEPT bottom-leading which is 6pt
- Trailing: 11pt `#707579` timestamp

**Quote Block (Inline Reply Preview)**
- Nested at TOP of bubble that's replying
- 3pt vertical accent bar on leading edge in sender's group color (or accent `#0088CC`)
- Background: 10% tint over bubble bg
- Sender name 13pt weight 600 in accent
- Quoted text 13pt regular, 2-line clamp

**File Attachment Card (inside bubble)**
- Horizontal card inside a bubble
- Leading 40pt circular icon (file type colored — PDF red, ZIP gray, music blue)
- File name 15pt weight 600 + size 13pt `#707579`
- Tap to download; shows circular progress ring overlay on icon

**Link Preview Card (inside bubble)**
- Image (if available) at top, full-width of bubble (16pt corner radius inherited, no rounding inside bubble)
- Source name 13pt weight 600 accent color
- Title 15pt weight 600
- Description 13pt `#707579`
- Appears above the message text, not below

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: `.regularMaterial` blur over `#FFFFFF` (light) / `#1C1C1C` (dark) at 92% opacity
- Tabs: Contacts, Calls, Chats, Settings (iOS default; may vary per user preference)
- Icon: 28pt (Telegram custom outlined icons, NOT SF Symbols)
- Active color: `#0088CC` (user accent)
- Inactive: `#8D8E93`
- Labels: SF Pro 10pt weight 500, always shown
- Badge: red circle with white count, top-right

**Top Nav (Chat Screen)**
- Height: 44pt + safe area
- Leading: back chevron + avatar (32pt) + contact name + typing/last-seen status (13pt gray)
- Trailing: video-call icon + phone-call icon + more-ellipsis (for secret chats: lock-icon replaces phone)
- Background: same blur treatment as tab bar
- Secret chat: small lock emoji next to the name — slight green-tint background

**Top Nav (Chat List)**
- Large-title "Chats" scroll-collapsing
- Leading: "Edit" text button
- Trailing: New chat icon (Telegram pencil-with-circle custom glyph)
- Search bar: pulls down on overscroll, 36pt pill

### Input Fields

**Compose Bar**
- Height: ~44pt single-line, grows to 120pt multiline
- Structure: leading attach + icon → multiline text field → trailing emoji face → sticker icon → voice-mic OR send
- Background: `#FFFFFF` light / `#1C1C1C` dark (elevated over chat canvas)
- Input field: inline, no pill container (different from WhatsApp) — just a text field
- Placeholder: "Message", 16pt `#A0A6AD`
- Bottom 1pt `#E5E5E5` divider from chat canvas

**Search Bar**
- Native iOS search bar (`.searchable` in SwiftUI)
- Background: `#F2F2F7` light / `#1C1C1C` dark
- Height: 36pt
- Cancel button text: accent color `#0088CC`

### Distinctive Components

**Animated Emoji / Sticker Playback**
Telegram's crown jewel. When a message contains exactly one emoji (or certain large emoji), it renders at a much bigger size and plays a Lottie animation on receipt/send.
- Size: Single emoji = 72pt rendered glyph
- Animation: Lottie/vector loop, ~2-3s duration, played once on arrival
- Some "interactive" emojis (heart, rocket) play a full-screen effect across both sender's and receiver's screens simultaneously
- Custom sticker packs: users and channels publish free or premium packs; render as 128pt animated vectors in bubbles

**Floating Voice/Music Mini Player**
Persistent pill that floats above the chat canvas when a voice message or music track is playing — similar to a Picture-in-Picture pill.
- Position: top of chat screen, just below nav bar
- Background: `#FFFFFF` light / `#1C1C1C` dark, with subtle shadow
- Height: 40pt
- Content: track title + horizontal waveform/progress + close X
- Tap: jump back to the playing message; swipe down: dismiss
- Persists across chat switches — this is unique to Telegram

**Swipe-from-Edge to Reply**
Not a swipe on the row — a gesture that lets you pan the entire bubble horizontally (usually to the left for outgoing, right for incoming) to trigger a reply.
- Threshold: ~60pt horizontal drag
- Visual: an arrow-with-hook icon appears at the drag edge as the bubble moves
- Haptic: soft tick at threshold
- Release past threshold: opens reply composer with that message quoted

**Silent Message Send (Long-Press Send)**
Long-pressing the send button opens a popover menu:
- Send Without Sound (no push notification sound on recipient)
- Schedule Message (pick a future time)
- Send When Online (auto-send when recipient comes online)
- This gesture is a core Telegram differentiator.

**Self-Destruct Timer (Secret Chats)**
Secret chats have a clock glyph next to the compose bar; tapping sets a timer (1s → 1 week). After recipient views, the message erases.
- Timer indicator: small countdown clock on read bubbles
- Secret chat header: lock emoji + "Secret Chat" + timer setting
- Screenshot notification: Telegram notifies the sender if the recipient screenshots a secret chat

**Reactions (Emoji Reaction Palette)**
- Long-press any message → reactions palette appears
- Free users: ~6 preset emojis (heart, thumbs-up, fire, laugh, surprise, cry)
- Premium users: any emoji + custom animated reactions
- Palette: horizontal floating capsule with reactions + a "+" for full picker
- Reaction chip appears below-trailing of bubble after pick; shows count if multiple users reacted
- Tap reaction chip to add yours; long-press to see who reacted

**Channel Broadcast vs Group**
- **Channel**: broadcast-only; users are "Subscribers" not "Members"; only admins post; no inline reply UI for most users; reactions + comments via linked-group
- **Group** / **Supergroup**: anyone can post; reply + mention + admin tools visible
- Channel avatar often has a custom accent color/background for identity
- Channels can have a custom gradient header (purple, rainbow) — the "channel color" feature

**Custom Chat Background**
Per-chat configurable: solid color, gradient, pattern tile, or photo. Accessible via chat settings.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56, 76
- Standard screen margin: 16pt horizontal
- Chat bubble margin from edge: 8pt
- Between bubbles same sender: 2pt; different sender: 8pt

### Grid & Container
- Content width: full device width
- Chat canvas: full bleed; bubbles max 80%
- Settings list: iOS grouped list with 16pt insets

### Whitespace Philosophy
- **Defer to iOS density**: Telegram follows iOS list patterns — 44pt minimum row heights, standard padding.
- **Chat canvas breathes**: Bubbles have 8pt gaps between different senders; the wallpaper/canvas is meant to be visible.
- **Floating elements pop**: The voice mini-player floats with shadow — it's meant to feel separate from the content.

### Border Radius Scale
- Square (0pt): Status bar overlays, rare
- Soft (6pt): Bubble tail corner (not fully pointed, a soft notch)
- Standard (10pt): Primary sheet CTAs, file cards
- Bubble (17pt): Main chat bubble corner radius
- Comfortable (20pt): Context menu, reactions palette
- Pill (22-28pt): Floating voice player, filter chips
- Full Circle (50%): Avatars, profile photos, send button (when designed as circle)

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow | List rows, bubbles on canvas |
| Subtle (1) | `rgba(0,0,0,0.08) 0 1px 3px` | Incoming bubbles (very subtle) |
| Floating (2) | `rgba(0,0,0,0.16) 0 4px 16px` | Voice mini-player, reactions palette |
| Sheet (3) | `rgba(0,0,0,0.25) 0 -12px 32px` | Action sheets, modals |
| Modal Overlay | `rgba(0,0,0,0.45)` backdrop | Context menu overlay, attachment picker |
| Nav Blur | `.regularMaterial` 92% | Top nav and bottom tab |

**Shadow Philosophy**: Telegram is flatter than most messengers; shadows are reserved for floating, detached elements (the voice pill, the reactions palette, the context menu popover). Chat bubbles are intentionally shadow-less; they sit flat on the canvas. The context-menu popover on long-press is the one place where heavy blur + dark backdrop creates real depth.

### Motion
- **Bubble send**: Slides up and lands with a slight overshoot spring (damping 0.6)
- **Animated emoji**: Lottie timeline 1.5-3s, plays once on arrival
- **Interactive emoji (heart/rocket)**: Full-screen animation sync'd across sender + receiver
- **Swipe-to-reply**: 60fps pan, haptic tick at threshold
- **Silent send long-press**: Standard iOS long-press 0.5s threshold → popover
- **Voice mini-player entry**: Slides down from top over 200ms
- **Reactions palette appearance**: Scales from center of bubble with spring, 300ms
- **Custom background pan**: On scroll, background may subtly parallax (can be disabled)
- **Context menu open**: Bubble "lifts" up with scale 1.05, backdrop blurs, menu fans out below

## 7. Do's and Don'ts

### Do
- Use `#0088CC` (Telegram Blue) as the default accent — but build the system so accent is a single variable that users can change
- Use `#2B86FD` for outgoing bubbles in default blue theme; support accent-tinted outgoing bubbles
- Respect user-selected chat backgrounds (solid, gradient, tile, photo)
- Use SF Pro + Telegram's proprietary outlined icon vocabulary (NOT SF Symbols alone)
- Offer OLED dark mode (`#000000`) as a distinct option from default dark
- Support inline Markdown-ish formatting (bold, italic, spoiler, code, pre)
- Animate single-emoji messages with Lottie playback
- Include the silent-send and scheduled-send long-press gesture on the send button
- Use the floating voice mini-player when audio is playing — pin it across chats
- Use swipe-from-edge (not swipe-from-row) to trigger reply

### Don't
- Don't hardcode accent color — theme it; the accent variable should cascade
- Don't use WhatsApp's asymmetric tail — Telegram bubble tails are SUBTLY notched (6pt), not pointed
- Don't use warm cream chat backgrounds; Telegram defaults to cool white/soft-blue or pure dark
- Don't use SF Symbols for everything — Telegram's tab bar uses outlined custom glyphs
- Don't dim the whole screen on long-press — only the backdrop around the context menu blurs
- Don't use shadows on chat bubbles — they're flat
- Don't hide the read-receipt timestamp — Telegram shows time + ticks consistently
- Don't auto-delete messages without the secret-chat UI indicating it
- Don't use green for read receipts (that's WhatsApp) — use accent-colored check-check
- Don't skip the scheduled-send option — it's a core differentiator

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max 78%; avatar 50pt in chat list |
| iPhone 13/14/15 | 390pt | Standard 54pt avatar, 80% bubble |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in voice mini-player |
| iPhone 15/16 Pro Max | 430pt | Avatar grows to 56pt |
| iPad | 768pt+ | Split view: chat list left, conversation right; bubbles constrained to 60% of right pane |

### Dynamic Type
- Bubble body, contact name, message preview: full scale
- Inline timestamp: fixed 11pt
- Tab labels: fixed 10pt
- Large title: scales to 40pt max
- Lottie emoji: scales with text size up to 96pt

### Orientation
- Chat list / Settings: portrait-locked on iPhone
- Calls: rotate to landscape
- Media viewer: rotate to landscape
- iPad: all orientations supported

### Touch Targets
- Send icon: 44pt effective hit
- Chat list row: 76pt
- Reactions palette emojis: 36pt glyph, 48pt hit
- Tab bar icons: 28pt glyph, 44pt hit
- Bubble swipe-to-reply threshold: 60pt drag

### Safe Area Handling
- Top: safe area honored
- Voice mini-player: sits just below nav bar, above chat content
- Bottom: compose bar hugs safe area
- Keyboard: native iOS push

## 9. Agent Prompt Guide

### Quick Color Reference
- Telegram Blue (accent): `#0088CC`
- Sky Blue (lighter): `#40A7E3`
- Bubble Blue (outgoing): `#2B86FD`
- Bubble Pressed: `#0071B0`
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#212121`
- Canvas (OLED): `#000000`
- Surface 1 (light): `#F7F7F7`
- Surface 1 (dark): `#1C1C1C`
- Surface 2 (dark): `#2C2C2E`
- Chat background (default blue tile): `#DBE7F4`
- Text primary (light): `#000000`
- Text secondary (light): `#707579`
- Text primary (dark): `#FFFFFF`
- Text secondary (dark): `#8D8E93`
- Online green: `#4DD364`
- Error red: `#E53935`
- Premium purple gradient: `#AE6FFD → #CE6BFF`

### Example Component Prompts
- "Create a SwiftUI Telegram outgoing bubble: max-width 80%, background #2B86FD, text #FFFFFF at SF Pro 17pt weight 400. Corner radius 17pt on all corners EXCEPT bottom-trailing which is 6pt (subtle notch). Inline trailing timestamp '10:24' 11pt white at 80% opacity + white double-check ticks 12pt."
- "Build the Telegram compose bar: no pill container — just a flat text field with leading paperclip attach icon (24pt #707579) and trailing emoji face + sticker + mic icons. When text present, trailing morphs to a single #0088CC paper-plane. Long-press send reveals the silent-send / scheduled-send popover menu."
- "Design the floating voice mini-player: 40pt tall rounded-pill (22pt radius) pinned just below the nav bar, background #FFFFFF light / #1C1C1C dark with rgba(0,0,0,0.16) 0 4px 16px shadow. Content: 13pt weight 600 track title + horizontal waveform/progress + close X. Swipe-down to dismiss; persists across chat switches."
- "Create the chat list row: 76pt height, 54pt circular avatar leading, 12pt gap, stacked contact name 17pt weight 600 + preview 15pt weight 400 #707579. Trailing: 13pt timestamp #707579 top-right, unread pill (#0088CC background, white text 13pt, 10pt corner radius), muted/pinned icons as applicable. Pinned rows have #F7F7F7 background tint."
- "Build a Telegram context menu on bubble long-press: bubble scales 1.05, backdrop blurs with .regularMaterial, reactions palette appears ABOVE bubble (floating capsule, 48pt emoji size, #FFFFFF background, 24pt corner radius, 16pt shadow), and action menu appears BELOW bubble with Reply, Copy, Forward, Edit, Delete — native iOS context menu with 20pt corner radius and #2C2C2E dark tint."
- "Design a Telegram self-destruct timer secret-chat header: nav bar has a 14pt lock.shield emoji next to contact name, a tiny countdown '10s' badge, and a subtle #C8E6C9 tint on the nav background. Chat canvas gets a green-tinged watermark lock glyph in the bottom corner at 10% opacity."

### Iteration Guide
1. Canvas defaults to `#FFFFFF` light / `#212121` dark — offer an OLED toggle for `#000000`
2. Accent color is a SINGLE variable — every blue surface reads from it, because users theme the app
3. Outgoing bubble `#2B86FD` default; support gradient outgoing `#2B86FD → #61B3FF` for gradient themes
4. Bubble corner radius is 17pt everywhere EXCEPT the tail corner (6pt notch — not fully pointed)
5. Bubbles are FLAT — no shadows. Shadows are only on the voice pill and context menu popover.
6. Typography is pure SF Pro at iOS HIG sizes; inline formatting (bold, italic, spoiler, code) via Markdown syntax
7. Swipe-from-edge triggers reply (not swipe-on-row) — bubble pans with user finger
8. Long-press send button opens silent-send / scheduled-send popover — don't skip this
9. Animated Lottie emoji + sticker playback on single-emoji messages — scale to 72pt glyph
10. Floating voice mini-player pins below nav bar when audio plays — persistent across chats
