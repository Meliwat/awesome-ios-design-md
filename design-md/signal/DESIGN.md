# Design System Inspiration of Signal (iOS)

## 1. Visual Theme & Atmosphere

Signal's iOS app is the calmest messaging surface on the platform, and the calm is the point. There is no feed, no Stories ring vying for a tap, no badge gamification, no ad slot, no vanity metric. The canvas is plain white (`#FFFFFF`) in light mode and a soft near-black (`#1B1B1B`) in dark mode, and the entire interface is built from three ingredients: a list of conversations, a thread of bubbles, and one accent — Signal Blue (`#3A76F0`). The restraint communicates the product thesis before a single word of copy: this app's job is to move your message privately and then get out of the way.

The chat thread is the heart of the design. Outgoing messages are Signal Blue bubbles with white text, right-aligned; incoming messages are a neutral gray (`#E9E9EB` light, `#2A2A2A` dark) with primary-color text, left-aligned. Bubbles use a generous corner radius (18pt) with a subtle tail-side tightening so a run of messages from the same sender visually groups. The send affordance is a single filled blue circle with an upward arrow — it appears only when there is text to send, sliding up from the composer with a brief spring. Nothing pulses, nothing demands attention. Privacy primitives are surfaced as quiet glyphs rather than marketing: a small lock for sealed-sender, a clock-ring chip for disappearing-message timers, a shield in safety-number screens.

Typography is **Inter** throughout — a neutral, highly legible humanist sans at weights 400 / 500 / 700. There is no display type, no editorial flourish; the largest text in the steady-state UI is a 17pt conversation name. The hierarchy is functional: conversation names at 17pt medium, message bodies at 16pt regular, timestamps and read receipts at 12pt regular gray. The app trusts plain, well-set text and a single trustworthy blue. The aesthetic could be summarized as "a utility you'd want handling a sensitive conversation": neutral, quiet, predictable, and visually honest — never trying to capture attention it doesn't need.

**Key Characteristics:**
- Privacy-minimal: zero ads, zero Stories pressure, zero vanity metrics — calm by design
- White light canvas (`#FFFFFF`) / soft-black dark canvas (`#1B1B1B`) — no pure-black harshness
- Signal Blue (`#3A76F0`) as the single accent — outgoing bubbles, send button, links, primary actions
- Outgoing = blue bubble + white text; incoming = neutral gray bubble + primary text
- 18pt bubble radius with same-sender grouping (tail-side tightening)
- Blue circular send button — appears only when there's text, slides up with a spring
- Disappearing-message timer chip — a clock-ring glyph beside the conversation name and on messages
- Sealed-sender lock glyph — privacy as a quiet icon, never a marketing banner
- Inter throughout, weights 400/500/700, no display type
- Bottom tabs: Chats / Calls / Stories / Settings

## 2. Color Palette & Roles

### Primary
- **Signal Blue** (`#3A76F0`): Outgoing message bubble, send button, links, primary buttons, active tab, selection.
- **Signal Blue Pressed** (`#2F5FCC`): Pressed/active state for blue buttons and the send circle.
- **Signal Blue Tint** (`#E7EEFD`): Light-mode selected row, blue-wash backgrounds (link previews on outgoing).

### Canvas & Surface (Light)
- **Canvas White** (`#FFFFFF`): Conversation list, chat background, navigation bars.
- **Surface Gray** (`#F5F5F5`): Grouped settings sections, search field, composer field background.
- **Incoming Bubble** (`#E9E9EB`): Left-aligned received message bubble.
- **Divider** (`#E5E5E5`): Hairline dividers between conversation rows, settings rows.
- **Text Primary** (`#000000`): Conversation names, incoming message text, primary labels.
- **Text Secondary** (`#6B6B6B`): Message previews, timestamps, read receipts, secondary labels.
- **Text Tertiary** (`#9A9A9A`): Placeholders, disabled labels.

### Canvas & Surface (Dark)
- **Dark Canvas** (`#1B1B1B`): Conversation list, chat background.
- **Dark Surface** (`#2A2A2A`): Grouped sections, search field, incoming bubble (dark).
- **Dark Divider** (`#3A3A3A`): Hairline dividers in dark mode.
- **Dark Text Primary** (`#FFFFFF`): Conversation names, incoming text (dark).
- **Dark Text Secondary** (`#9A9A9A`): Previews, timestamps (dark).
- **Dark Incoming Bubble** (`#2A2A2A`): Received message bubble in dark mode.

### Message-Specific
- **Outgoing Bubble** (`#3A76F0`): Sent message background (both modes).
- **Outgoing Text** (`#FFFFFF`): Text on outgoing bubbles (both modes).
- **Outgoing Meta** (`#CBD9F9`): Timestamp + read check inside an outgoing bubble (soft blue-white).
- **Read Check Blue** (`#3A76F0`): Double-check "read" indicator on outgoing.

### Semantic
- **Disappearing Timer** (`#6B6B6B`): Clock-ring chip color (neutral — privacy is quiet, not loud).
- **Sealed Sender Lock** (`#6B6B6B`): Lock glyph tint in message info.
- **Safety Shield** (`#3A76F0`): Verified safety-number shield accent.
- **Error Red** (`#D7263D`): Failed-to-send retry, destructive "Delete" actions.
- **Success Green** (`#3AB54A`): "Saved", verified-checkmark confirmations.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (humanist sans — neutral, highly legible at message sizes)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace (safety numbers)**: `'SF Mono', ui-monospace, Menlo, monospace` — the 60-digit safety number is grouped monospace
- **Weights**: 400 (regular), 500 (medium), 700 (bold)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Inter | 28pt | 700 | 1.2 | -0.3pt | "Chats" nav large title |
| Conversation Name | Inter | 17pt | 500 | 1.3 | 0pt | Row title in the chat list |
| Thread Title | Inter | 17pt | 600 | 1.25 | 0pt | Contact name in the chat header |
| Message Body | Inter | 16pt | 400 | 1.4 | 0pt | The bubble text — core reading size |
| Message Preview | Inter | 15pt | 400 | 1.3 | 0pt | Last-message snippet in chat list |
| Section Header | Inter | 13pt | 600 | 1.2 | 0.4pt | "CONTACTS", grouped settings headers (uppercase) |
| Timestamp (Row) | Inter | 13pt | 400 | 1.2 | 0pt | Time on a conversation row |
| Bubble Meta | Inter | 12pt | 400 | 1.2 | 0pt | Time + read check inside a bubble |
| Read Receipt | Inter | 12pt | 400 | 1.2 | 0pt | "Read 9:41 PM" under last outgoing |
| Button (Primary) | Inter | 16pt | 600 | 1.0 | 0pt | Blue "Send"/"Next"/"Create" labels |
| Button (Text) | Inter | 16pt | 400 | 1.0 | 0pt | Blue text actions ("Edit", "Cancel") |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab bar labels |
| Timer Chip | Inter | 12pt | 500 | 1.0 | 0pt | Disappearing-message duration ("1w", "8h") |
| Safety Number | SF Mono | 15pt | 400 | 1.5 | 0.5pt | 60-digit grouped safety number |
| System Message | Inter | 13pt | 400 | 1.35 | 0pt | "Messages are end-to-end encrypted" centered note |

### Principles
- **One typeface, three weights**: Inter 400/500/700 only — no display, no condensed, no italic for UI
- **Message body at 16pt / line-height 1.4**: comfortable reading without wasted vertical space
- **System notices are centered, gray, small**: encryption + safety messages whisper, never shout
- **Safety number is monospace and grouped**: 5-digit chunks for human verification
- **Dynamic Type respected** on names, message bodies, previews; tab labels and bubble meta are layout-fixed
- **No color in text except links and the active tab** — blue is structural, content text is neutral

## 4. Component Stylings

### Buttons

**Send Button (Signature)**
- Shape: Circle, diameter 32pt (composer-trailing)
- Background: `#3A76F0`
- Icon: upward arrow glyph `arrow.up`, 16pt, `#FFFFFF`
- Visibility: hidden when composer is empty; slides up + fades in (180ms spring) when text exists
- Pressed: scale 0.90, background `#2F5FCC`, soft haptic
- Voice-note alternate: when empty, a microphone glyph replaces send (same circle)

**Primary Button ("Next", "Create Group", "Verify")**
- Background: `#3A76F0`
- Text: `#FFFFFF`, Inter 16pt weight 600
- Padding: 14pt vertical, full-width (28pt horizontal inset)
- Corner radius: 10pt
- Pressed: `#2F5FCC`, scale 0.99

**Text Button ("Edit", "Cancel", "Add")**
- Inter 16pt weight 400 (600 if it's the confirming action)
- Color: `#3A76F0`
- No background; 44pt hit area; pressed opacity 0.5

**Destructive Action ("Delete", "Block", "Leave")**
- Text/label color: `#D7263D`
- Same geometry as text button or list-row trailing

**Icon Action (Attach, Camera, Voice, Call, Video)**
- Glyph 22pt, hit area 44pt
- Default: `#6B6B6B` (light) / `#9A9A9A` (dark)
- Active/primary (call accept): `#3AB54A`; (call in header): `#3A76F0`

### Cards & Containers

**Conversation Row**
- Height: 72pt
- Layout: 48pt circular avatar (leading) → name (17pt w500) + last-message preview (15pt w400 `#6B6B6B`, 1-line clamp) → trailing column: timestamp (13pt `#6B6B6B`) + unread pill
- Unread pill: `#3A76F0` filled circle, white count, min 20pt; muted convo shows a bell-slash glyph instead
- Disappearing-on conversation: a small clock-ring glyph trails the name
- Background: canvas; pressed `#F5F5F5` (light) / `#2A2A2A` (dark)
- Divider: hairline `#E5E5E5` inset to text origin (not under avatar)

**Message Bubble — Outgoing**
- Background: `#3A76F0`
- Text: `#FFFFFF`, Inter 16pt w400
- Padding: 9pt vertical, 13pt horizontal
- Corner radius: 18pt, with the bottom-right corner tightened to 6pt on the last bubble of a same-sender run (tail)
- Meta: time + read check inside, bottom-right, 12pt `#CBD9F9`
- Max width: 75% of thread width; right-aligned with 12pt trailing inset

**Message Bubble — Incoming**
- Background: `#E9E9EB` (light) / `#2A2A2A` (dark)
- Text: `#000000` (light) / `#FFFFFF` (dark), Inter 16pt w400
- Corner radius: 18pt, bottom-left tightened to 6pt on last of run (tail)
- Meta: time below or trailing, 12pt `#6B6B6B`
- Max width: 75%; left-aligned with 12pt leading inset; group sender avatar shown once at the run's bottom

**Disappearing-Message Timer Chip (Signature)**
- A pill: clock-ring glyph + duration label (Inter 12pt w500)
- Color: `#6B6B6B` glyph + text; background transparent (chat header) or `#F5F5F5` (settings)
- Placed beside the thread title and in the conversation settings
- Selecting opens a duration picker (Off / 30s / 5m / 1h / 8h / 1d / 1w / custom)

**System / Encryption Note**
- Centered, full-width, Inter 13pt w400 `#6B6B6B`
- "Messages and calls are end-to-end encrypted." with a tiny lock glyph leading
- No background; 12pt vertical padding; appears at the top of every new thread

**Settings Group**
- Grouped list on `#F5F5F5` (light) / `#1B1B1B` (dark) base, rows on canvas
- Row height 50pt, leading SF-style glyph 22pt `#3A76F0`, title 16pt w400, trailing chevron / toggle
- Section header 13pt w600 uppercase `#6B6B6B`, 0.4pt tracking

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` (light) / `#1B1B1B` (dark), top hairline `#E5E5E5` / `#3A3A3A`
- Tabs: Chats, Calls, Stories, Settings
- Icon 24pt; active `#3A76F0`, inactive `#6B6B6B` / `#9A9A9A`
- Labels: Inter 10pt w500, always shown
- Chats tab: unread total badge (red dot or count) `#3A76F0`

**Chat List Top Bar**
- Large title "Chats" (28pt w700) collapsing to inline on scroll
- Trailing: compose glyph (pencil-in-square) 22pt `#3A76F0`
- Below title: search field — `#F5F5F5` fill, 36pt, corner radius 10pt, placeholder "Search"
- Leading (on scroll): profile avatar 28pt circular

**Thread Header**
- Height: 44pt + safe area, canvas background, bottom hairline
- Leading: back chevron 22pt `#3A76F0` + 32pt avatar + name (17pt w600) + (timer chip if on)
- Trailing: video-call glyph + audio-call glyph, 22pt `#3A76F0`, 20pt gap
- Tap name/avatar: opens conversation settings

### Input Fields

**Composer**
- Container: pill, `#F5F5F5` (light) / `#2A2A2A` (dark), min height 36pt, corner radius 18pt, 12pt horizontal inset from edges
- Leading: plus/attach glyph 22pt `#6B6B6B`
- Field: Inter 16pt w400, placeholder "Signal message" `#9A9A9A`, grows to 5 lines then scrolls
- Trailing inside pill: camera glyph; outside pill: the blue send circle (or mic when empty)
- Focus: no border change; keyboard accessory stays pinned

**Search Field**
- Background: `#F5F5F5` (light) / `#2A2A2A` (dark)
- Height: 36pt, corner radius 10pt
- Leading magnifier 16pt `#6B6B6B`, placeholder "Search" `#9A9A9A`
- Focus: cursor `#3A76F0`, optional "Cancel" text button appears trailing

### Distinctive Components

**Privacy-Minimal Stance**
- No ads, no algorithmic feed, no Stories pressure (Stories tab exists but is opt-in and quiet), no read-streaks, no like-counts
- Every screen is either a list, a thread, or a settings group — the absence of clutter is the design

**Send Button Slide-Up**
- The single most-used affordance: a 32pt blue circle that does not exist until you type
- Appears with a 180ms spring (slide up + fade); reverts to a mic glyph when the field empties

**Disappearing-Message Timer Chip**
- A clock-ring glyph + duration, neutral gray, beside the thread title — privacy as ambient state, not a banner
- When active, sent/received bubbles carry a faint timer ring that visually "drains"

**Sealed Sender & Safety Number**
- Sealed-sender shown as a tiny lock in message-info detail (never on the bubble itself)
- Safety-number screen: 60 digits in 12 monospace groups + a scannable QR, with a blue "Verified" shield toggle

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56
- Bubble inner padding: 9pt vertical, 13pt horizontal
- Inter-bubble gap: 2pt within a same-sender run, 8pt between senders/groups
- Row inset: avatar leading 16pt, text origin 76pt (divider starts here)

### Grid & Container
- Single column everywhere — list, thread, settings
- Bubble max width: 75% of thread width
- Thread side insets: 12pt; avatar (group) reserves 28pt on the leading side
- No multi-column grids; iPad uses a master-detail split (list left, thread right)

### Whitespace Philosophy
- **Calm density**: 72pt conversation rows are roomy but not wasteful; threads breathe with 8pt sender gaps
- **The absence is the feature**: no decorative chrome competes with the conversation
- **Centered system notes get vertical air**: encryption/safety messages are isolated, never inline-crowded
- **Quiet by default**: nothing is bolder or more saturated than it must be

### Border Radius Scale
- Soft (6pt): the tightened tail corner on the last bubble of a run
- Standard (10pt): primary buttons, search field, settings rows' selected highlight
- Bubble (18pt): message bubbles, composer pill
- Pill (20pt+): unread count pill, timer chip
- Circle (50%): avatars, send button, call buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Conversation rows, bubbles, settings — the default everywhere |
| Bubble (Level 0) | No shadow (color separation only) | Bubbles rely on fill contrast, not elevation |
| Bar Hairline (Level 1) | `rgba(0,0,0,0.06) 0 0.5px 0` | Tab bar + thread header on scroll |
| Sheet (Level 2) | `rgba(0,0,0,0.16) 0 -8px 28px` | Attachment sheet, reaction picker, context menu |
| Toast (Level 3) | `rgba(0,0,0,0.18) 0 6px 20px` | "Saved to gallery", "Link copied" |

**Shadow Philosophy**: Signal is almost entirely flat. Bubbles separate by fill contrast, not by shadow — a shadow on a bubble would feel like a skeuomorphic toy and undercut the utility tone. The only real elevation is transient: the attachment sheet, the reaction picker, the context menu. Even those use restrained, soft shadows. Flatness reinforces the message: this is plumbing, not a showroom.

### Motion
- **Send**: button slides up + fades in 180ms spring (damping 0.8); on tap, bubble slides up into the thread from the composer with a 250ms ease
- **Send circle press**: scale 1.0 → 0.90 → 1.0, soft haptic
- **Reaction picker**: emoji row scales in 200ms spring above the long-pressed bubble
- **Timer drain**: disappearing message shows a thin ring that animates from full to empty over its lifetime
- **Tab switch**: instant crossfade, no slide
- **Pull-to-refresh**: none (list is live) — minimal system spinner only where applicable

## 7. Do's and Don'ts

### Do
- Keep it privacy-minimal: no ads, no algorithmic feed, no vanity counts — calm is the brand
- Use white (`#FFFFFF`) / soft-black (`#1B1B1B`) canvases — never pure `#000000`
- Reserve Signal Blue (`#3A76F0`) for outgoing bubbles, the send button, links, and primary actions
- Make outgoing bubbles blue with white text; incoming neutral gray with primary text
- Show the send circle only when there's text; revert to a mic glyph when empty
- Render disappearing-timer state as a quiet gray clock-ring chip, never a marketing banner
- Group same-sender bubbles by tightening the tail corner to 6pt
- Set everything in Inter at weights 400/500/700 only

### Don't
- Don't add ads, Stories pressure, streaks, or like-counts — the absence is the feature
- Don't introduce a second accent color — blue is the only one
- Don't put shadows on bubbles — they separate by fill contrast, not elevation
- Don't use pure black canvas in dark mode — `#1B1B1B` is the soft-black target
- Don't show the send button when the composer is empty
- Don't shout about privacy — sealed-sender and timers are quiet glyphs, not banners
- Don't use display-size type — the 28pt large title is the ceiling
- Don't color body text — only links and the active tab are blue

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 64pt rows, bubble max 78% width |
| iPhone 13/14/15 | 390pt | Standard — 72pt rows |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the header |
| iPhone 15/16 Pro Max | 430pt | Larger avatars (52pt), wider thread |
| iPad | 768pt+ | Master-detail split: list (left, 360pt) + thread (right) |

### Dynamic Type
- Conversation names, message bodies, previews: full scale
- Message body caps at ~22pt; bubble grows with content
- Bubble meta, timestamps, tab labels: layout-fixed (do not scale)
- Safety number: scales one step then clamps (grouping must stay aligned)

### Orientation
- Chat list / thread / settings: **portrait + landscape** (landscape widens thread, keeps single column on phone)
- iPad: split view in both orientations
- Media viewer: rotates freely

### Touch Targets
- Send circle: 32pt visual, 44pt effective hit
- Conversation rows: full 72pt row tappable
- Composer attach/camera glyphs: 44pt hit
- Tab items: 24pt icon, 49pt hit

### Safe Area Handling
- Top: large title + search respect safe area / Dynamic Island
- Bottom: composer + tab bar respect home indicator; keyboard pushes composer above keyboard inset
- Sides: 12pt thread insets, 16pt list insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light/dark): `#FFFFFF` / `#1B1B1B`
- Surface (light/dark): `#F5F5F5` / `#2A2A2A`
- Incoming bubble (light/dark): `#E9E9EB` / `#2A2A2A`
- Divider (light/dark): `#E5E5E5` / `#3A3A3A`
- Text primary (light/dark): `#000000` / `#FFFFFF`
- Text secondary: `#6B6B6B` / `#9A9A9A`
- Signal Blue (outgoing / send / links): `#3A76F0`
- Signal Blue pressed: `#2F5FCC`
- Outgoing text: `#FFFFFF`; outgoing meta: `#CBD9F9`
- Error red: `#D7263D`; success green: `#3AB54A`

### Example Component Prompts
- "Create a SwiftUI Signal conversation row: 72pt height, 48pt circular avatar leading, name 'Renata Vogel' Inter 17pt weight 500 #000000, last-message preview 'See you at 8 then' Inter 15pt w400 #6B6B6B (1-line clamp). Trailing column: timestamp '9:41 PM' Inter 13pt #6B6B6B and an unread pill — a #3A76F0 circle with white '3'. Divider hairline #E5E5E5 inset to text origin. Pressed background #F5F5F5."
- "Build a Signal outgoing message bubble: background #3A76F0, text #FFFFFF Inter 16pt w400, padding 9pt vertical 13pt horizontal, corner radius 18pt with the bottom-right corner tightened to 6pt for the tail. Inside bottom-right: '9:41 PM' + a double-check, 12pt #CBD9F9. Max width 75% of thread, right-aligned, 12pt trailing inset."
- "Build the Signal composer + send button: a pill #F5F5F5, min height 36pt, corner radius 18pt, leading plus glyph 22pt #6B6B6B, field placeholder 'Signal message' Inter 16pt #9A9A9A, trailing camera glyph inside. Outside the pill: a 32pt #3A76F0 circle with a white up-arrow that is HIDDEN when the field is empty and slides up + fades in over 180ms when text exists; reverts to a microphone glyph when emptied. Pressed: scale 0.90, #2F5FCC."
- "Design the Signal disappearing-message timer chip: a small pill with a clock-ring glyph + duration label '1w' in Inter 12pt w500, all in #6B6B6B, transparent background, placed right of the thread title. Tapping opens a duration picker (Off / 30s / 5m / 1h / 8h / 1d / 1w / custom). Privacy is a quiet glyph here — never a colored banner."
- "Build the Signal bottom tab bar: 50pt + safe area, background #FFFFFF (light) / #1B1B1B (dark), top hairline #E5E5E5. Tabs Chats / Calls / Stories / Settings, icons 24pt, active #3A76F0, inactive #6B6B6B, labels Inter 10pt w500. Chats shows a #3A76F0 unread badge. Add the centered top-of-thread system note 'Messages and calls are end-to-end encrypted.' Inter 13pt #6B6B6B with a tiny leading lock."

### Iteration Guide
1. Stay privacy-minimal — no ads, no feed, no streaks, no vanity counts; the absence is the design
2. Canvas is `#FFFFFF` light / `#1B1B1B` dark — never pure black
3. Signal Blue (`#3A76F0`) is the ONLY accent — outgoing bubble, send button, links, primary actions
4. Outgoing = blue bubble + white text; incoming = neutral gray bubble + primary text
5. The send circle does not exist until there's text — it slides up; empty state is a mic glyph
6. Disappearing-timer is a quiet gray clock-ring chip; sealed-sender is a tiny lock — never banners
7. Everything flat — bubbles separate by fill contrast, real elevation only on sheets/menus
8. Inter only, weights 400/500/700, no display type — the 28pt large title is the ceiling
