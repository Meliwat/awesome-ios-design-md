# Design System Inspiration of Messenger (iOS)

## 1. Visual Theme & Atmosphere

Messenger's iOS app is the most expressive mainstream messaging surface — playful, colorful, and built for reactions rather than restraint. Where a privacy-first messenger whispers, Messenger performs. The defining visual is the outgoing message bubble: not a flat color but a vivid diagonal gradient sweeping from electric blue (`#0A7CFF`) through violet (`#9D4EDD`) into pink (`#FF5CA0`). The gradient is anchored to the *conversation*, not the bubble — it flows continuously down a run of your messages as if they were drawn on one ribbon, so a thread looks like a stream of color rather than disconnected chips. Incoming messages stay neutral gray, keeping the visual energy on "you."

The canvas itself is calm white (`#FFFFFF`) in light mode and true black (`#000000`) in dark mode — Messenger uses pure black in dark mode (unlike privacy-minimal apps), which makes the gradient bubbles glow against it. Chrome is round and friendly: circular "chat head" avatars everywhere, pill search fields, fully-rounded bubbles (18pt). The signature interaction is the reactions popover — long-press any message and a floating row of six emoji (👍 ❤️ 😆 😮 😢 😡) springs up above the bubble with a bouncy scale animation; tapping one drops a small reaction badge onto the bubble's corner. The send affordance is a big friendly blue thumb/arrow that doubles as a one-tap "like" (the iconic thumbs-up) when the composer is empty.

Typography is **Inter / SF** at weights 400 / 600 / 700 — clean and legible, deferring to the color and motion for personality. Conversation names are 17pt semibold, message bodies 16pt regular, the active-now green dot and reaction badges carry meaning at small sizes. Bottom tabs are minimal (Chats / Marketplace / Stories). The overall feel is "a conversation that's fun to be in": rounded, gradient-lit, reaction-forward, and motion-rich — the opposite of utilitarian, intentionally so.

**Key Characteristics:**
- Gradient outgoing bubble — diagonal `#0A7CFF → #9D4EDD → #FF5CA0`, anchored to the conversation and flowing down the whole run
- Incoming bubbles stay neutral gray — energy stays on "you"
- White light canvas (`#FFFFFF`) / true-black dark canvas (`#000000`) — black makes the gradient glow
- Reactions popover — long-press → floating row of 6 emoji with a bouncy spring; tap drops a corner badge
- Circular chat-head avatars everywhere — friendly, round, ever-present
- Big-thumb send — a friendly blue circle that is a one-tap thumbs-up "like" when the composer is empty
- Active-now green dot — a `#31D158` presence ring on avatars
- Fully-rounded 18pt bubbles, pill search, round everything
- Inter / SF at 400/600/700 — type defers to color and motion
- Bottom tabs: Chats / Marketplace / Stories

## 2. Color Palette & Roles

### Primary (the Gradient)
- **Gradient Blue** (`#0A7CFF`): Start of the outgoing-bubble gradient, send button, links, primary actions, active tab.
- **Gradient Violet** (`#9D4EDD`): Mid-stop of the outgoing-bubble gradient.
- **Gradient Pink** (`#FF5CA0`): End-stop of the outgoing-bubble gradient.
- **Blue Pressed** (`#0866D6`): Pressed state for solid-blue controls (send, primary buttons).
- **Like Thumb** (`#0A7CFF`): The thumbs-up reaction + the empty-composer "like" button.

### Canvas & Surface (Light)
- **Canvas White** (`#FFFFFF`): Chat list, thread background, navigation.
- **Surface Gray** (`#F1F1F2`): Search field, grouped sections, composer field background.
- **Incoming Bubble** (`#F1F1F2`): Left-aligned received message bubble (light).
- **Divider** (`#E4E6EB`): Hairline dividers between list rows.
- **Text Primary** (`#050505`): Conversation names, incoming text, primary labels.
- **Text Secondary** (`#65676B`): Previews, timestamps, secondary labels.
- **Text Tertiary** (`#8A8D91`): Placeholders, disabled labels.

### Canvas & Surface (Dark)
- **Dark Canvas** (`#000000`): Chat list, thread background (true black — makes gradient glow).
- **Dark Surface** (`#1C1C1D`): Search field, grouped sections.
- **Dark Incoming Bubble** (`#303030`): Received message bubble (dark).
- **Dark Divider** (`#3A3B3C`): Hairline dividers in dark mode.
- **Dark Text Primary** (`#E4E6EB`): Conversation names, incoming text (dark).
- **Dark Text Secondary** (`#B0B3B8`): Previews, timestamps (dark).

### Message-Specific
- **Outgoing Text** (`#FFFFFF`): Text on the gradient bubble (both modes).
- **Outgoing Meta** (`#FFFFFFCC`): Time + delivery status inside an outgoing bubble (80% white).
- **Reaction Badge BG** (`#FFFFFF` light / `#000000` dark): Pill behind the corner reaction emoji + count, with a hairline ring.

### Semantic
- **Active Now Green** (`#31D158`): Presence dot/ring on avatars (online indicator).
- **Typing Dots** (`#65676B`): The animated "…" typing indicator in a gray bubble.
- **Unread Blue** (`#0A7CFF`): Unread conversation dot + bold name.
- **Error Red** (`#FA383E`): Failed-to-send, destructive actions.
- **Success Green** (`#31A24C`): "Sent", confirmation toasts.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (matches Messenger's clean grotesque tone; SF Pro is the native fallback)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Emoji**: system color emoji (Apple Color Emoji) — reactions are real emoji glyphs
- **Weights**: 400 (regular), 600 (semibold), 700 (bold)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Inter | 28pt | 700 | 1.2 | -0.3pt | "Chats" nav large title |
| Conversation Name | Inter | 17pt | 600 | 1.3 | 0pt | Row title in chat list (bold 700 if unread) |
| Thread Title | Inter | 16pt | 600 | 1.25 | 0pt | Contact/group name in chat header |
| Message Body | Inter | 16pt | 400 | 1.4 | 0pt | The bubble text — core reading size |
| Message Preview | Inter | 15pt | 400 | 1.3 | 0pt | Last-message snippet (semibold if unread) |
| Section Header | Inter | 13pt | 700 | 1.2 | 0.2pt | Grouped section labels |
| Timestamp (Row) | Inter | 13pt | 400 | 1.2 | 0pt | Time on a conversation row |
| Bubble Meta | Inter | 12pt | 400 | 1.2 | 0pt | Time + delivery inside/under a bubble |
| Reaction Count | Inter | 12pt | 600 | 1.0 | 0pt | Number beside a corner reaction emoji |
| Button (Primary) | Inter | 16pt | 600 | 1.0 | 0pt | Blue primary button labels |
| Button (Text) | Inter | 16pt | 600 | 1.0 | 0pt | Blue text actions |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Active Now | Inter | 12pt | 400 | 1.2 | 0pt | "Active now" / "Active 5m ago" |
| System Message | Inter | 13pt | 400 | 1.35 | 0pt | "You named the group …" centered note |

### Principles
- **Type defers to color and motion**: the gradient + reactions carry personality, so text stays clean and quiet
- **Unread is bold, not colored**: an unread conversation goes weight 700, with a small blue dot — never colored text
- **Message body at 16pt / line-height 1.4**: comfortable and compact
- **Emoji are first-class**: reactions render as full-color system emoji at meaningful sizes (24pt in the popover)
- **Dynamic Type respected** on names and bodies; bubble meta, reaction counts, tab labels are layout-fixed
- **Weights 400/600/700 only** — no light, no display type

## 4. Component Stylings

### Buttons

**Big-Thumb Send (Signature)**
- Shape: Circle, diameter 32pt (composer-trailing)
- Empty composer: a thumbs-up glyph `hand.thumbsup.fill` 20pt in `#0A7CFF`, no fill background (tap = send a 👍 "like")
- With text: a filled `#0A7CFF` circle with a white paper-plane/arrow 16pt
- Transition: glyph morphs thumbs-up → filled-send with a 180ms spring as text appears
- Pressed: scale 0.90, `#0866D6` if filled, soft haptic
- Long-press the empty-state thumb: scales the 👍 up for a "big like" (super-size reaction)

**Primary Button ("Create", "Send Invite")**
- Background: `#0A7CFF` (solid blue — the gradient is reserved for bubbles)
- Text: `#FFFFFF`, Inter 16pt weight 600
- Padding: 13pt vertical, full-width (16pt horizontal inset)
- Corner radius: 20pt (pill-ish, friendly)
- Pressed: `#0866D6`, scale 0.99

**Text Button ("Edit", "Done")**
- Inter 16pt weight 600, color `#0A7CFF`
- No background; 44pt hit area; pressed opacity 0.5

**Reaction Pill (on a bubble)**
- A small rounded pill anchored to the bubble's bottom corner
- Background: `#FFFFFF` (light) / `#000000` (dark) with a 1pt `#E4E6EB` / `#3A3B3C` ring
- Content: the emoji (16pt) + optional count (Inter 12pt w600 `#65676B`)
- Multiple reactions stack horizontally; tap opens the reaction detail sheet

**Icon Action (Camera, Photo, Mic, GIF, Call, Video, Info)**
- Glyph 22pt, hit area 44pt
- Default: `#0A7CFF` (Messenger tints action glyphs blue, not gray)
- In-thread header call/video: `#0A7CFF`

### Cards & Containers

**Conversation Row**
- Height: 72pt
- Layout: 56pt circular avatar with optional green active-ring (leading) → name (17pt w600, 700 if unread) + last-message preview (15pt w400 `#65676B`, 1-line clamp) → trailing: timestamp (13pt) + unread blue dot
- Active-now: a `#31D158` 14pt dot on the avatar's bottom-right with a 2pt canvas-color ring
- Background: canvas; pressed `#F1F1F2` (light) / `#1C1C1D` (dark)
- Divider: hairline `#E4E6EB` inset to text origin

**Message Bubble — Outgoing (Gradient, Signature)**
- Background: a diagonal linear gradient `#0A7CFF → #9D4EDD → #FF5CA0` (≈135°)
- **Conversation-anchored**: the gradient spans the full vertical extent of the visible thread, each bubble shows the slice of the ribbon at its position — a run of your messages reads as one continuous color flow
- Text: `#FFFFFF`, Inter 16pt w400
- Padding: 9pt vertical, 14pt horizontal
- Corner radius: 18pt, with the tail-side corner tightened to 6pt on the last bubble of a same-sender run
- Meta: time + delivery (sent/delivered/seen avatar) below the bubble, 12pt `#FFFFFFCC` or `#65676B`
- Max width: 72% of thread width; right-aligned with 10pt trailing inset

**Message Bubble — Incoming**
- Background: `#F1F1F2` (light) / `#303030` (dark) — flat, neutral
- Text: `#050505` (light) / `#E4E6EB` (dark), Inter 16pt w400
- Corner radius: 18pt, bottom-left tightened to 6pt on last of run
- Group sender avatar (28pt) shown once at the run's bottom-left
- Max width: 72%; left-aligned with 10pt leading inset

**Reactions Popover (Signature)**
- Triggered by long-press on any bubble
- A floating capsule above the bubble: 6 emoji (👍 ❤️ 😆 😮 😢 😡) at 30pt, 12pt gaps, plus a "+" to open the full picker
- Background: `#FFFFFF` (light) / `#1C1C1D` (dark), corner radius 28pt (full pill), shadow `rgba(0,0,0,0.18) 0 8px 28px`
- Enter: scales 0.7 → 1.0 with a spring (damping 0.6) + each emoji has a 20ms staggered pop
- On selection: chosen emoji animates down and lands as a corner reaction badge with a bounce

**Typing Indicator**
- A gray incoming-style bubble containing three `#65676B` dots that bounce in sequence (the "…")
- Same radius/inset as an incoming bubble

**Stories Tray**
- Horizontal row at the top of Chats (or its own tab): circular avatars with a gradient ring for unseen Stories
- "Your story" tile with a blue "+" badge leading

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` (light) / `#000000` (dark), top hairline `#E4E6EB` / `#3A3B3C`
- Tabs: Chats, Marketplace, Stories
- Icon 26pt; active `#0A7CFF`, inactive `#65676B`
- Labels: Inter 10pt w600, always shown
- Chats: unread total badge `#FA383E` (red) with white count

**Chat List Top Bar**
- Large title "Chats" (28pt w700), profile avatar (32pt circular) leading on scroll
- Trailing: new-message compose glyph (pencil) 22pt `#0A7CFF`
- Below title: pill search field — `#F1F1F2` fill, 36pt, corner radius 18pt, placeholder "Search"
- Optional Stories tray row beneath search

**Thread Header**
- Height: 44pt + safe area, canvas background, bottom hairline
- Leading: back chevron 24pt `#0A7CFF` + 36pt avatar (with active dot) + name (16pt w600) + "Active now" (12pt `#65676B`)
- Trailing: audio-call + video-call glyphs, 22pt `#0A7CFF`, 18pt gap
- Tap name/avatar: opens conversation info

### Input Fields

**Composer**
- A row of round action glyphs (camera / photo / mic / GIF) in `#0A7CFF`, then the pill field
- Pill: `#F1F1F2` (light) / `#1C1C1D` (dark), min height 36pt, corner radius 18pt
- Field: Inter 16pt w400, placeholder "Aa" `#8A8D91`, grows to 5 lines then scrolls
- Trailing: emoji glyph inside the pill; outside: the big-thumb send (👍 when empty)
- Focus: action glyphs collapse into a single chevron to give the field room (Messenger's signature collapse)

**Search Field**
- Background: `#F1F1F2` (light) / `#1C1C1D` (dark)
- Height: 36pt, corner radius 18pt (full pill)
- Leading magnifier 16pt `#65676B`, placeholder "Search" `#8A8D91`
- Focus: cursor `#0A7CFF`, "Cancel" text button appears trailing

### Distinctive Components

**Conversation-Anchored Gradient Bubble**
- The single most identifying element: outgoing messages are a blue→violet→pink gradient that flows continuously down the whole run, not per-bubble
- Implementation: render the gradient on a layer the size of the message stack and mask it to the union of outgoing bubble shapes

**Reactions Popover**
- Long-press any bubble → a springy floating row of 6 emoji + "+"
- Bouncy enter (scale 0.7 → 1 spring, staggered emoji pop); selection lands as a corner badge with a bounce
- The defining "fun" interaction of the app

**Big-Thumb Send / One-Tap Like**
- Empty composer shows a blue 👍 — tapping it instantly sends a thumbs-up; long-press makes it a giant like
- Morphs into a filled send button when you type

**Active-Now Presence**
- A `#31D158` dot/ring on avatars across the list, header, and Stories tray — Messenger foregrounds presence

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 16, 20, 24, 32, 44, 56
- Bubble inner padding: 9pt vertical, 14pt horizontal
- Inter-bubble gap: 2pt within a same-sender run, 8pt between senders
- Row inset: avatar leading 16pt, text origin 80pt (divider starts here)

### Grid & Container
- Single column everywhere — list, thread
- Bubble max width: 72% of thread width
- Thread side insets: 10pt; group avatar reserves 28pt on the leading side
- Stories tray: horizontal scroll, 64pt circular tiles, 12pt gap
- iPad: master-detail split (list left, thread right)

### Whitespace Philosophy
- **Friendly density**: 72pt rows, rounded everything — approachable, never clinical
- **The gradient is the focal point**: keep incoming bubbles flat and gray so the color stays on "you"
- **Reactions get room to perform**: the popover floats clear above the bubble with shadow separation
- **Black background in dark mode** is intentional — it makes the gradient luminous

### Border Radius Scale
- Soft (6pt): the tightened tail corner on the last bubble of a run
- Standard (12pt): info cards, attachment thumbnails
- Bubble (18pt): message bubbles, composer pill, search pill
- Pill (20pt): primary buttons
- Popover (28pt): the reactions popover capsule
- Circle (50%): avatars, send button, chat heads, reaction badges

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Conversation rows, bubbles, canvas |
| Bubble (Level 0) | No shadow (color/fill separation) | Bubbles separate by gradient vs gray, not elevation |
| Bar Hairline (Level 1) | `rgba(0,0,0,0.06) 0 0.5px 0` | Tab bar + thread header on scroll |
| Reaction Badge (Level 1) | `rgba(0,0,0,0.10) 0 1px 3px` | The corner reaction pill lifts slightly off the bubble |
| Popover (Level 3) | `rgba(0,0,0,0.18) 0 8px 28px` | Reactions popover, context menu |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Attachment sheet, reaction-detail sheet |

**Shadow Philosophy**: Bubbles are flat — they separate by the gradient-vs-gray contrast, not shadow. The only persistent shadow is the subtle lift under a corner reaction badge so it reads as stuck *onto* the bubble. Real elevation is reserved for the reactions popover and sheets, where a soft 28px shadow makes them float convincingly above the playful canvas.

### Motion
- **Reactions popover enter**: capsule scale 0.7 → 1.0 spring (damping 0.6), emoji pop in with a 20ms stagger
- **Reaction land**: selected emoji translates from the popover to the bubble corner + bounce 1.0 → 1.25 → 1.0
- **Bubble pop**: a newly sent bubble scales 0.85 → 1.0 spring (damping 0.65) as it enters
- **Big-thumb morph**: thumbs-up ↔ filled send, 180ms spring as composer text toggles
- **Big like**: long-press the empty thumb → 👍 scales up smoothly; release sends a giant reaction
- **Active dot**: a gentle pulse on first appearance, then static
- **Tab switch**: instant crossfade

## 7. Do's and Don'ts

### Do
- Render the outgoing bubble as a `#0A7CFF → #9D4EDD → #FF5CA0` gradient anchored to the conversation, flowing down the whole run
- Keep incoming bubbles flat neutral gray — energy stays on "you"
- Use white (`#FFFFFF`) light canvas and TRUE black (`#000000`) dark canvas — black makes the gradient glow
- Make the reactions popover bouncy: spring enter, staggered emoji pop, landing bounce
- Show a blue thumbs-up in the empty composer that one-tap sends a 👍 and morphs to send on text
- Put a `#31D158` active-now dot on avatars — Messenger foregrounds presence
- Use circular chat-head avatars everywhere; round all bubbles to 18pt
- Keep type clean (Inter 400/600/700) — color and motion carry the personality

### Don't
- Don't make outgoing bubbles a flat single color — the conversation-anchored gradient is the brand
- Don't gradient the incoming bubbles — they stay flat gray
- Don't use soft-black in dark mode — Messenger uses pure `#000000` so the gradient luminesces
- Don't make the reactions popover stiff — it must bounce and stagger
- Don't show the filled send button when the composer is empty — it's a thumbs-up then
- Don't color body text for unread — go bold (700) + a small blue dot instead
- Don't add a second solid accent — blue is the UI accent; the gradient is bubble-only
- Don't use display-size type — the 28pt large title is the ceiling

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 64pt rows, bubble max 78%, popover emoji 26pt |
| iPhone 13/14/15 | 390pt | Standard — 72pt rows, popover emoji 30pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the header |
| iPhone 15/16 Pro Max | 430pt | Larger avatars (60pt), wider thread |
| iPad | 768pt+ | Master-detail split: list (left, 360pt) + thread (right) |

### Dynamic Type
- Conversation names, message bodies, previews: full scale
- Message body caps at ~22pt; bubble grows with content
- Bubble meta, reaction counts, tab labels: layout-fixed (do not scale)
- Reactions popover emoji: fixed size (gesture-sensitive layout)

### Orientation
- Chat list / thread: **portrait + landscape** (landscape widens the thread; gradient re-spans the new height)
- iPad: split view in both orientations
- Media viewer / video call: rotates freely

### Touch Targets
- Big-thumb send: 32pt visual, 44pt effective hit
- Reactions popover emoji: 44pt effective hit each (even though rendered ~30pt)
- Conversation rows: full 72pt row tappable
- Tab items: 26pt icon, 49pt hit

### Safe Area Handling
- Top: large title + search respect safe area / Dynamic Island
- Bottom: composer + tab bar respect home indicator; composer rises with the keyboard
- Sides: 10pt thread insets, 16pt list insets
- Reactions popover repositions to stay on-screen near edges

## 9. Agent Prompt Guide

### Quick Color Reference
- Gradient: `#0A7CFF` → `#9D4EDD` → `#FF5CA0` (≈135°, outgoing bubble only)
- UI blue (send / links / actions): `#0A7CFF`; pressed `#0866D6`
- Canvas (light/dark): `#FFFFFF` / `#000000` (true black)
- Surface (light/dark): `#F1F1F2` / `#1C1C1D`
- Incoming bubble (light/dark): `#F1F1F2` / `#303030`
- Divider (light/dark): `#E4E6EB` / `#3A3B3C`
- Text primary (light/dark): `#050505` / `#E4E6EB`
- Text secondary: `#65676B` / `#B0B3B8`
- Active-now green: `#31D158`; error red: `#FA383E`

### Example Component Prompts
- "Create a SwiftUI Messenger conversation row: 72pt height, 56pt circular avatar leading with a #31D158 active-now dot (14pt, 2pt canvas ring) at its bottom-right. Name 'Theo Marchetti' Inter 17pt weight 600 #050505, last-message preview 'sent a photo' Inter 15pt #65676B (1-line clamp). Trailing: timestamp '9:41 AM' Inter 13pt #65676B. If unread: name weight 700 + a 8pt #0A7CFF dot. Pressed background #F1F1F2."
- "Build a Messenger outgoing gradient bubble: background is a ≈135° linear gradient from #0A7CFF through #9D4EDD to #FF5CA0, ANCHORED to the conversation (the gradient spans the full visible thread height; this bubble shows its slice so a run of messages reads as one continuous ribbon). Text #FFFFFF Inter 16pt w400, padding 9pt vertical 14pt horizontal, corner radius 18pt with the bottom-right tightened to 6pt for the tail. Max width 72%, right-aligned."
- "Design the Messenger reactions popover: triggered by long-press on a bubble. A floating capsule above it with 6 emoji 👍 ❤️ 😆 😮 😢 😡 at 30pt, 12pt gaps, plus a '+'. Background #FFFFFF (light) / #1C1C1D (dark), corner radius 28pt, shadow rgba(0,0,0,0.18) 0 8px 28px. Enter: scale 0.7 → 1.0 spring (damping 0.6) with a 20ms staggered emoji pop. On tap: the chosen emoji flies down and lands as a corner badge on the bubble with a 1.0 → 1.25 → 1.0 bounce."
- "Build the Messenger composer with big-thumb send: a row of #0A7CFF action glyphs (camera, photo, mic, GIF) then a #F1F1F2 pill (corner radius 18pt, placeholder 'Aa' #8A8D91, emoji glyph trailing inside). Outside the pill: a 32pt send affordance that is a blue 👍 thumbs-up when the field is EMPTY (tap = send a like) and morphs via a 180ms spring into a filled #0A7CFF circle with a white send arrow when text exists. Long-press the empty thumb scales the 👍 up for a 'big like'."
- "Build the Messenger bottom tab bar: 50pt + safe area, background #FFFFFF (light) / #000000 (true black, dark). Tabs Chats / Marketplace / Stories, icons 26pt, active #0A7CFF, inactive #65676B, labels Inter 10pt w600. Chats shows a #FA383E unread badge with a white count."

### Iteration Guide
1. Outgoing bubble = conversation-anchored `#0A7CFF → #9D4EDD → #FF5CA0` gradient flowing down the whole run — this is THE brand
2. Incoming bubbles stay flat neutral gray — keep the energy on "you"
3. Canvas is `#FFFFFF` light / TRUE `#000000` dark — black makes the gradient glow
4. Reactions popover must bounce: spring enter (damping 0.6), staggered emoji pop, landing bounce
5. Empty composer shows a blue 👍 (one-tap like); it morphs to a filled send on text
6. Put `#31D158` active-now dots on avatars — presence is foregrounded
7. Round everything: 18pt bubbles, circular avatars/chat-heads, pill fields
8. Type stays clean (Inter 400/600/700) — color and motion carry personality; unread = bold + blue dot, never colored text
