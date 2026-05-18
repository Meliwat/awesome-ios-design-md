# Design System Inspiration of Viber (iOS)

## 1. Visual Theme & Atmosphere

Viber is a Rakuten-owned messaging and free-calling app whose entire visual identity is built around one color: **Viber Purple** (`#7360F2`). Where iMessage is blue and WhatsApp is green, Viber owns purple — it's in the logomark, the outgoing bubble, every primary button, the call screen, the tab-bar selection, and the signature "Free Viber Call" banner. The app's atmosphere is friendly, rounded, and call-first: Viber started as a free VoIP app before it was a messenger, and the UI never lets you forget that voice and video calling are one tap away. Open any conversation and a purple-gradient "Free Viber Call" banner often sits at the top, and the chat header always carries phone + video icons.

The canvas is clean white (`#FFFFFF`) in light mode and a deliberately aubergine-tinted near-black (`#121118`) in dark mode — Viber's dark theme isn't a neutral gray, it carries a faint purple cast that ties back to the brand. Conversations are a vertical stack of rounded bubbles: outgoing in Viber Purple `#7360F2` with white text and a small ~5pt tail on the bottom-right; incoming in a light gray `#EDEBF5` (light) / `#26232F` (dark) with primary-color text and a tail on the bottom-left. Bubbles use a ~16pt radius. A timestamp and a **double-check receipt** sit at the bottom-right of every outgoing message: one gray check = sent, two gray checks = delivered, two **violet** checks = seen. This three-state check system is one of Viber's most recognizable affordances.

The other signature pillar is the **sticker market**. Viber's emoji/sticker keyboard opens a full storefront of animated sticker packs — branded, seasonal, purchasable — and stickers render in-thread as large ~96pt borderless artwork (no bubble background). The emoji button in the input bar opens this market. Combined with the free-call banner and the green "online" presence dot, the sticker economy is core to Viber's identity. Other distinctive components: the Calls tab with a recent-calls list, Viber Out (paid landline calling) entry, Communities/Channels, message reactions (a thumbs-up-led emoji strip on long-press), and disappearing "secret" messages with a timer.

Typography is a friendly, slightly rounded humanist sans. Viber's brand face is a custom "Viber" type family; the closest free Google substitute is **Manrope** (geometric, rounded terminals, warm) or Nunito Sans. Hierarchy is product-UI: large screen titles, bold contact headlines, 15pt message body, small gray metadata. Color beyond purple is minimal — green `#46C26A` for online/accept-call, red `#F0506E` for decline/destructive and unread badges, neutral grays for chrome. Purple is the only saturated brand surface.

**Key Characteristics:**
- Viber Purple (`#7360F2`) is the entire brand — outgoing bubble, every CTA, call screen, tab selection
- White canvas (`#FFFFFF`) light / aubergine-tinted near-black (`#121118`) dark
- Rounded ~16pt bubbles: outgoing purple, incoming gray (`#EDEBF5` / `#26232F`), small ~5pt tail
- Three-state check receipt: 1 gray = sent, 2 gray = delivered, 2 violet (`#8F7DF7`) = seen
- "Free Viber Call" banner — purple→deep-violet (`#59267C`) gradient, call-first messaging
- Sticker market — full storefront keyboard; stickers render as ~96pt borderless in-thread artwork
- Call-first chrome — phone + video icons always in the chat header; dedicated Calls tab
- Green online presence dot (`#46C26A`); red unread badges (`#F0506E`)
- Reactions strip on long-press (thumbs-up led emoji row)
- Viber Out, Communities/Channels, secret/disappearing messages with timer
- Friendly rounded humanist sans (Viber brand face → Manrope substitute)
- Bottom tab bar: Chats / Calls / Explore / More with purple active tint

## 2. Color Palette & Roles

### Primary (Interactive)
- **Viber Purple** (`#7360F2`): The brand. Outgoing bubble, primary buttons, tab selection, call screen, send button, links.
- **Viber Purple Deep** (`#665CAC`): Logo wordmark, deeper purple in gradients and pressed surfaces.
- **Viber Purple Pressed** (`#5B4ACC`): Pressed state of purple controls.
- **Viber Violet** (`#8F7DF7`): Seen-receipt checks, lighter accent, gradient top stop, links on dark.
- **Banner Deep Violet** (`#59267C`): Bottom stop of the free-call banner gradient.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Thread background, primary light canvas.
- **Incoming Bubble** (`#EDEBF5`): Light lavender-gray incoming bubble.
- **Surface Gray** (`#F4F3F8`): Grouped list background, search field, settings.
- **Row Pressed** (`#E9E7F2`): Pressed conversation/list rows.
- **Divider** (`#E4E2EC`): Hairline separators between list rows and header.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121118`): Aubergine-tinted near-black thread background (NOT neutral gray).
- **Dark Surface 1** (`#1C1A24`): Nav bar, input bar, tab bar, elevated surfaces.
- **Dark Surface 2** (`#26232F`): Incoming bubble, search field, sheets.
- **Dark Divider** (`#322F3C`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#1A1825`): Incoming bubble text, primary labels.
- **Text on Purple** (`#FFFFFF`): Outgoing bubble text — always white on purple.
- **Text Secondary** (`#7D7A8C` light / `#9D98AC` dark): Timestamps, list preview, contact subtitle.
- **Text Tertiary** (`#A8A5B5` light / `#6C6779` dark): Single sent-check gray, placeholder, disabled.
- **Text Primary Dark** (`#F3F1F8`): Incoming bubble text and labels on dark.

### Semantic
- **Online / Accept Green** (`#46C26A`): Online presence dot, accept-call button, "delivered to Viber".
- **Error / Decline Red** (`#F0506E`): Decline-call, delete, destructive actions, unread count badge.
- **Seen Violet** (`#8F7DF7`): Two-check "Seen" receipt color.
- **Link** (`#7360F2` light / `#9B8CFF` dark): Inline links inside bubbles.
- **Selection Highlight** (`#7360F2` @ 18%): Text selection within a bubble.

### Receipt Color Matrix

| State | Glyph | Color (Light) | Color (Dark) |
|-------|-------|---------------|--------------|
| Sending (clock) | clock | `#A8A5B5` | `#6C6779` |
| Sent | one check | `#A8A5B5` | `#6C6779` |
| Delivered | double check | `#7D7A8C` | `#9D98AC` |
| Seen | double check | `#8F7DF7` | `#8F7DF7` |
| Failed | exclamation | `#F0506E` | `#F0506E` |

## 3. Typography Rules

### Font Family
- **Brand**: `Viber` — a custom friendly humanist sans with rounded terminals (proprietary; not redistributable).
- **Recommended Free Substitute**: `Manrope` (by Mikhail Sharanda, SIL OFL) — geometric humanist with soft, rounded feel that closely matches Viber's warmth; or `Nunito Sans` (SIL OFL) as a rounder alternative.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Manrope', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: proportional by default; tabular in timestamps and call durations.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Manrope | 32pt | 800 | 1.2 | -0.4pt | "Chats" / "Calls" screen title |
| Title 1 | Manrope | 26pt | 800 | 1.2 | -0.3pt | "Free Viber Call" / sheet headers |
| Title 3 | Manrope | 22pt | 700 | 1.25 | -0.2pt | Group name, large contact name |
| Headline | Manrope | 17pt | 700 | 1.3 | -0.2pt | Contact name in chat header / list row title |
| Body / Bubble | Manrope | 15pt | 400 | 1.32 | 0pt | Message text |
| Body Emphasis | Manrope | 15pt | 600 | 1.32 | 0pt | Sender name in group bubbles, mentions |
| List Preview | Manrope | 14pt | 500 | 1.35 | 0pt | Last-message preview in chat list |
| Footnote | Manrope | 13pt | 600 | 1.35 | 0pt | "Seen 9:41", "Delivered", section labels |
| Caption | Manrope | 11pt | 600 | 1.35 | 0.1pt | Timestamp separators, presence ("online") |
| Button | Manrope | 16pt | 700 | 1.0 | 0pt | Primary / call buttons |
| Tab Label | Manrope | 11pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Badge | Manrope | 11pt | 700 | 1.0 | 0pt | Unread count badges |

### Principles
- **Friendly and rounded**: weights skew heavy (700/800 for titles and buttons) to feel approachable and confident, not corporate.
- **Body stays light**: message body at 15pt regular keeps the chat readable and dense.
- **Dynamic Type supported**: titles, headlines, body, list previews scale; timestamps, tab labels, badges, receipt glyphs stay fixed.
- **White on purple is non-negotiable**: outgoing bubble text is always `#FFFFFF`.
- **Tracking near-zero**: Manrope/Viber face is comfortable at default tracking; only tiny captions get a small positive value.

## 4. Component Stylings

### Buttons

**Primary Button**
- Shape: Pill / rounded rectangle, 24pt corner radius (fully rounded for short labels)
- Background: `#7360F2`
- Text: `#FFFFFF`, Manrope 16pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `#5B4ACC` + scale 0.98
- Disabled: `#7360F2` @ 35% opacity

**Call Button (Accept / Free Call)**
- Shape: pill, 24pt radius (or 60pt circle on the call screen)
- Background: `#46C26A` (accept/free) / `#F0506E` (decline)
- Icon + text: `#FFFFFF`, Manrope 15pt weight 700
- Used in the free-call banner CTA and the in-call accept/decline controls

**Outline Button (secondary)**
- Background: transparent
- Border: 1.5pt `#7360F2`
- Text: `#7360F2` (light) / `#8F7DF7` (dark), Manrope 15pt weight 700
- Corner radius: 24pt
- Pressed: `#7360F2` @ 10% fill

**Text Button**
- Background: transparent
- Text: `#7360F2` / `#8F7DF7`, Manrope 15pt weight 600 ("View Stickers", "Edit")
- Pressed: opacity 0.5

**Send Button (circular)**
- 36pt circle, background `#7360F2`, paper-plane glyph `#FFFFFF` 16pt
- When the field is empty it's a mic icon (push-to-talk voice message) instead

### Message Bubble (the core atom)

**Outgoing Bubble**
- Background: `#7360F2`
- Text: `#FFFFFF`, Manrope 15pt w400
- Padding: 9pt vertical, 14pt horizontal
- Corner radius: 16pt, bottom-right corner at 5pt (tail) when last in a same-sender run
- Alignment: trailing, max width ~80% of thread
- Footer (inside or just below): timestamp 10pt + receipt checks, right-aligned

**Incoming Bubble**
- Background: `#EDEBF5` light / `#26232F` dark
- Text: `#1A1825` / `#F3F1F8`, Manrope 15pt w400
- Corner radius: 16pt, bottom-left corner at 5pt (tail)
- Alignment: leading; in groups, sender name in 15pt w600 `#7360F2` above the first bubble

**Grouping**
- Same sender consecutive: 4pt gap, only last bubble shows the tail
- Sender change: 8pt gap
- Day separator: centered 11pt caption, `secondaryLabel`, 8pt vertical margin

**States**
- Sending: clock glyph receipt, bubble at ~70% opacity until ack
- Failed: red exclamation, tap to resend
- With reaction: small reaction chip docked at the bottom-near corner
- Sticker / GIF: rendered as ~96pt borderless artwork, no bubble background, timestamp+receipt below
- Reply quote: a thin purple left-bar + quoted snippet inside the bubble

### Three-State Check Receipt

- Position: bottom-right of the outgoing bubble, after the timestamp
- Sent: single check, `#A8A5B5` / `#6C6779` (~16×12pt)
- Delivered: double overlapping check, `#7D7A8C` / `#9D98AC`
- Seen: double check, `#8F7DF7` (violet) — the brand "they read it" moment
- Failed: `#F0506E` exclamation circle

### Free Viber Call Banner

- Position: top of the conversation (under the header) or in the call-promo card
- Background: linear gradient `#7360F2` → `#59267C` (135°)
- Corner radius: 14pt, 12pt internal padding
- Leading: white phone glyph 18pt
- Text: title "Free Viber Call" Manrope 13pt w700 `#FFFFFF`, subtitle 11pt `rgba(255,255,255,0.8)`
- Trailing: pill CTA "Call" — `rgba(255,255,255,0.22)` fill, white 12pt w700

### Sticker Market

- Opened from the emoji/sticker button in the input bar
- Full-screen-width storefront panel above the keyboard
- Top: horizontal pack tabs (recent, owned, store) + a search field
- Body: grid of sticker packs; each pack a card with cover art + name + "Free"/price pill
- In-thread sticker: ~96pt borderless animated artwork, timestamp + receipt below, no bubble

### Navigation

**Chat Header**
- Height: 56pt + safe area, hairline `divider` bottom
- Leading: back chevron (`chevron.left` 17pt `#7360F2`)
- Center-left: 36pt circular avatar + name (Manrope 15pt w700) stacked over presence ("online" 11pt `#46C26A`)
- Trailing: phone icon + video icon (22pt `#7360F2`) — call-first
- Tapping the header opens the contact/info screen

**Chat List (Chats tab)**
- Large title "Chats" (32pt w800), search field, compose FAB or nav button
- Row: 72pt — leading 52pt circular avatar with optional green presence dot, title (17pt w700), preview (14pt `secondaryLabel`, 1-line), trailing timestamp (13pt) + unread count badge (red `#F0506E` pill)
- Swipe: Delete (red), More

**Bottom Tab Bar**
- Height: 50pt + safe area, background `#FFFFFF` / `#1C1A24`, hairline top divider
- Tabs (4): Chats, Calls, Explore, More
- Icon 22pt; active = `#7360F2` filled glyph + label; inactive = `#A8A5B5` / `#6C6779`
- Labels: Manrope 11pt w600 always shown; unread badge on Chats/Explore (red pill)

### Input Fields

**Compose Bar**
- Background: `#FFFFFF` / `#1C1A24`, hairline top divider, 8/16pt padding
- Leading: `+` 22pt attachment (camera, gallery, file, location, contact)
- Center: pill field — 18pt radius, `#F4F3F8` / `#26232F` fill, placeholder "Type a message" `tertiaryLabel`
- Trailing-inner: emoji/sticker face glyph 22pt (opens sticker market)
- Trailing: 36pt circular send button (`#7360F2`, paper-plane); mic when empty (hold-to-record voice)

**Search Bar**
- Height: 36pt, `#F4F3F8` / `#26232F`, 10pt radius
- Leading `magnifyingglass` 15pt `tertiaryLabel`, placeholder "Search"

### Distinctive Components

**Three-State Check Receipt** — gray→gray→violet checks; the most-recognized Viber affordance.

**Free Call Banner** — purple gradient, always nudging toward free Viber-to-Viber calling; call-first DNA.

**Sticker Market** — a full storefront in the keyboard; stickers render large and borderless in-thread.

**Reactions Strip** — long-press a bubble → emoji row (thumbs-up led: 👍 ❤️ 😂 😮 😢 😡); reaction docks as a small chip.

**Presence Dot** — green `#46C26A` ring/dot on the avatar when the contact is online.

**Call Screen** — full purple gradient background, large circular avatar, accept (green) / decline (red) circular controls, duration timer in tabular figures.

**Secret / Disappearing Message** — a timer chip on the bubble; self-destructs after viewing.

**Communities / Channels** — broadcast surfaces with a distinct header and follower count.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 44
- Same-sender bubble gap: 4pt
- Sender-change gap: 8pt
- Thread horizontal inset: 14pt, bubble max width ~80%
- Bubble internal padding: 9pt vertical / 14pt horizontal

### Grid & Container
- iPhone: full-width thread, 14pt gutters; bubble max ~80% of width
- Sticker artwork: ~96pt square, left/right aligned by sender
- iPad: split — chat list (~340pt) + conversation; conversation content max ~680pt centered
- Banners and headers: full-width, safe-area aware

### Whitespace Philosophy
- **Friendly density**: 4pt within a run, 8pt on sender change — conversational but compact
- **Purple is the only saturated surface**: chrome stays neutral so the outgoing bubble and CTAs pop
- **Call-first whitespace**: the free-call banner and header call icons always have room — calling is never buried
- **Stickers breathe**: borderless ~96pt artwork floats free of bubble chrome for an expressive feel

### Border Radius Scale
- Square (0pt): full-bleed media (rare)
- Tail (5pt): the pinched bubble corner on the sender's edge
- Subtle (10pt): search bar, list thumbnails
- Standard (14pt): free-call banner, cards, reply-quote container
- Bubble (16pt): the message bubble body radius
- Comfortable (20pt): sticker pack cards, sheets
- Pill (24pt / 500pt): primary buttons, input field, badges
- Circle (50%): avatars, send button, call controls, presence dot

## 6. Depth & Elevation

Viber is mostly flat with soft, low shadows on floating surfaces and a signature purple gradient on call/banner.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Bubbles, list rows, thread content |
| Raised (Level 1) | `rgba(115,96,242,0.10) 0 2px 8px` | Free-call banner, sticker pack cards |
| Floating (Level 2) | `rgba(0,0,0,0.14) 0 6px 20px` (light) / 1pt border on dark | Reactions strip, context menu, FAB |
| Sheet (Level 3) | `rgba(0,0,0,0.18) 0 -4px 24px` | Attachment sheet, sticker market, share |
| Modal Overlay | `rgba(0,0,0,0.45)` dim | Behind call screen, modals, sheets |

**Shadow Philosophy**: Viber uses tinted purple shadows on brand surfaces (banner, sticker cards) so elevation feels on-brand rather than neutral. Floating menus and the reactions strip get a soft neutral shadow so they clearly hover. On dark mode shadows fade, so floating surfaces gain a faint 1pt `divider` border. The call screen and free-call banner are the only places a full purple→violet gradient appears.

### Motion
- **Bubble send**: outgoing bubble slides up + scales 0.85 → 1.0 from the input field, `spring(response: 0.32, damping: 0.78)` + light haptic
- **Receipt transition**: sent → delivered → seen checks crossfade in 0.2s; seen recolors gray → violet with a subtle pulse
- **Reactions strip**: long-press (~450ms) → strip scales 0.7 → 1.0 spring; selecting docks the chip with a pop + soft haptic
- **Sticker play**: animated stickers loop (Lottie/APNG), auto-play on appear, ~2s loop
- **Sticker market open**: slides up from bottom over the keyboard, 0.3s ease-out
- **Free-call banner**: gentle entrance fade + 6pt slide-down; subtle shimmer on the gradient (optional)
- **Incoming call**: full-screen purple gradient fades in; accept/decline buttons rise with spring; ringer haptic loop
- **Tab switch**: instant; active-icon fill + color crossfade 0.15s
- **Typing**: "typing…" text in the header subtitle (Viber uses text, not dots, in many builds)
- **Haptics**: light impact on send, soft on reaction dock, success on call connect, warning on call decline

## 7. Do's and Don'ts

### Do
- Make Viber Purple `#7360F2` the single brand color — outgoing bubble, every CTA, tab selection, call screen
- Use white (`#FFFFFF`) light canvas / aubergine-tinted near-black (`#121118`) dark — purple-cast, not neutral gray
- Render outgoing bubbles purple, incoming gray (`#EDEBF5` / `#26232F`), ~16pt radius with a ~5pt tail
- Implement the three-state check: 1 gray = sent, 2 gray = delivered, 2 violet `#8F7DF7` = seen
- Keep calling one tap away — phone + video icons in every chat header, dedicated Calls tab, free-call banner
- Render stickers as large ~96pt borderless artwork in-thread, no bubble background
- Use the purple→deep-violet (`#59267C`) gradient only on the call screen and free-call banner
- Use green `#46C26A` for online/accept, red `#F0506E` for decline/unread badges
- Use a friendly rounded humanist sans (Viber face → Manrope), heavy weights for titles/buttons
- White text on purple bubbles, always

### Don't
- Don't use a neutral gray dark canvas — Viber's dark mode is aubergine-tinted `#121118`
- Don't tint incoming bubbles purple — they're neutral gray; only outgoing is brand purple
- Don't make the seen receipt gray — "Seen" is violet `#8F7DF7`, that recolor is the signature
- Don't wrap stickers in a bubble — they're borderless ~96pt artwork
- Don't bury calling behind menus — it's call-first; header + Calls tab + banner are mandatory
- Don't introduce a second brand accent — purple is it; green/red are semantic only
- Don't use thin type for titles/buttons — Viber's friendliness comes from heavy 700/800 weights
- Don't use a hard drop shadow on bubbles — bubbles are flat; only banners/menus elevate
- Don't put the purple gradient everywhere — reserve it for call screen + free-call banner
- Don't use black text on a purple bubble — outgoing text is always `#FFFFFF`
- Don't animate aggressively — motion is soft spring, 0.2–0.32s

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max ~300pt; compact header (no Dynamic Island) |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above header |
| iPhone 15/16 Pro Max | 430pt | Wider bubbles (~344pt), larger sticker artwork |
| iPad (portrait) | 768pt | Split: chat list (~340pt) + conversation; content max ~680pt centered |
| iPad (landscape) | 1024pt+ | Persistent list + conversation; call screen full-pane |

### Dynamic Type
- Titles, headline, body, list preview: full scale
- Timestamps, receipt glyphs, tab labels, badges, presence text: FIXED
- Sticker artwork: fixed ~96pt (does not scale with text)

### Orientation
- Conversation supports rotation; bubbles reflow keeping the ~80% cap
- iPad keeps split view in both orientations; call screen uses the full pane
- Video call rotates to landscape with PiP support

### Touch Targets
- Send / `+` / emoji buttons: 22–36pt glyph, 44pt hit
- Bubble: full bubble is the long-press (reactions) target
- Reaction glyphs: 28pt glyph, 44pt hit
- Header call icons: 44pt hit each
- List row / tab: full-row / full-cell tap

### Safe Area Handling
- Top: header respects safe area + Dynamic Island
- Bottom: input bar + tab bar respect home indicator; thread scrolls above keyboard
- Keyboard: sticker market and compose bar ride above the keyboard
- Sides: 14pt bubble gutters; full-bleed only for media

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121118` (aubergine-tinted, not neutral)
- Outgoing bubble: `#7360F2`
- Incoming bubble: `#EDEBF5` light / `#26232F` dark
- Bubble text (outgoing): `#FFFFFF`
- Text primary: `#1A1825` light / `#F3F1F8` dark
- Text secondary: `#7D7A8C` light / `#9D98AC` dark
- Seen receipt (violet): `#8F7DF7`
- Sent/delivered receipt: `#A8A5B5` → `#7D7A8C`
- Free-call banner gradient: `#7360F2` → `#59267C`
- Online / accept: `#46C26A`
- Decline / unread badge: `#F0506E`
- Link: `#7360F2` / `#9B8CFF`

### Example Component Prompts
- "Build a Viber outgoing bubble in SwiftUI: background `#7360F2`, text `#FFFFFF` Manrope 15pt regular, padding 9pt vertical / 14pt horizontal, corner radius 16pt with `.bottomTrailing` at 5pt (tail) when last in a same-sender run. Right-aligned, max width 80%. Footer row: timestamp 10pt + the three-state check receipt, right-aligned. Incoming variant: background `#EDEBF5` (light) / `#26232F` (dark), text `label`, tail on `.bottomLeading`."

- "Create the Viber three-state check receipt: a small ~16×12pt glyph after the timestamp. Sent = single check `#A8A5B5`/`#6C6779`; Delivered = double overlapping check `#7D7A8C`/`#9D98AC`; Seen = double check `#8F7DF7` (violet). Crossfade between states in 0.2s; on Seen, recolor gray → violet with a subtle pulse."

- "Build the Viber 'Free Viber Call' banner: full-width card under the chat header, linear gradient `#7360F2` → `#59267C` at 135°, 14pt corner radius, 12pt padding. Leading white phone glyph 18pt. Title 'Free Viber Call' Manrope 13pt w700 `#FFFFFF`, subtitle 11pt `rgba(255,255,255,0.8)`. Trailing pill CTA 'Call' with `rgba(255,255,255,0.22)` fill, white 12pt w700. Soft purple-tinted shadow `rgba(115,96,242,0.10) 0 2px 8px`."

- "Render an in-thread Viber sticker: ~96pt borderless animated artwork (Lottie/APNG), no bubble background, aligned by sender. Below it, right-aligned for outgoing: timestamp 10pt + three-state check receipt. Auto-play the animation on appear with a ~2s loop."

- "Create the Viber compose bar: background `#FFFFFF`/`#1C1A24`, hairline top divider. Leading `+` 22pt attachment. Center: pill field 18pt radius, `#F4F3F8`/`#26232F` fill, placeholder 'Type a message' `tertiaryLabel`. Inner-trailing: emoji/sticker face glyph 22pt that opens the sticker market. Trailing: 36pt circular send button `#7360F2` with a paper-plane glyph `#FFFFFF`; show a mic icon (hold-to-record voice) when the field is empty."

- "Render the Viber chat header: 56pt + safe area, hairline divider. Leading `chevron.left` 17pt `#7360F2`. Center-left: 36pt circular avatar + name Manrope 15pt w700 stacked over presence text 'online' 11pt `#46C26A`. Trailing: phone icon + video icon 22pt `#7360F2` (call-first). Tapping the header opens the contact info screen."

### Iteration Guide
1. Viber Purple `#7360F2` is the entire brand — outgoing bubble, every CTA, tab selection, call screen; there is no second accent
2. Dark canvas is aubergine-tinted near-black `#121118`, NOT neutral gray — the purple cast ties to the brand
3. Incoming bubbles are neutral gray `#EDEBF5` / `#26232F`; only the outgoing bubble is purple
4. The three-state check (1 gray sent, 2 gray delivered, 2 violet `#8F7DF7` seen) is the signature receipt — the violet recolor is the moment
5. Viber is call-first: phone + video in every chat header, a dedicated Calls tab, and the purple→violet free-call banner
6. Stickers render as ~96pt borderless artwork in-thread — never wrapped in a bubble
7. Typography is a friendly rounded humanist sans (Manrope substitute) with heavy 700/800 titles and buttons
8. The purple→deep-violet `#59267C` gradient is reserved for the call screen and free-call banner only
9. Green `#46C26A` = online/accept; red `#F0506E` = decline/unread badge; everything else is neutral
10. Bubbles are flat ~16pt with a ~5pt tail; depth is soft tinted-purple shadows on banners and neutral shadows on floating menus
