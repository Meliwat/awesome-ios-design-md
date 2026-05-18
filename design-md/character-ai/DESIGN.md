# Design System Inspiration of Character.AI (iOS)

## 1. Visual Theme & Atmosphere

Character.AI's iOS app exists to do one thing exceptionally well: let you have a believable, immersive conversation with an AI **persona**. Every design decision flows from that. The canvas is a near-black neutral (`#0F0F10`) — dark-first, so nothing competes with the conversation for attention. The signature screen is the **chat**: you open a character and you're greeted by a single circular **character avatar** (a soft radial gradient — blue or lilac), the character's name and one-line description centered beneath it ("Aria — A curious starship navigator · by @lumen"), and then the dialogue itself flowing upward as a stream of message bubbles. The first AI message is the character's **greeting** — its in-character hello.

The defining component is the **asymmetric chat bubble**. AI messages and user messages are deliberately set in *very close* grey tones (`#1C1C1F` for the AI, `#26262A` for the user) — Character.AI does **not** color-code speakers with a bright "user blue." Instead, the speaker is signaled by a single **tucked corner**: the AI bubble has a sharp 4pt top-left corner (the "tail" pointing back at the avatar) with the other three corners at 18pt; the user bubble mirrors it with a sharp 4pt top-right corner. This subtle, almost typographic differentiation keeps the screen calm and book-like, which is the point — you're meant to feel like you're *reading a story you're co-writing*. Inside bubbles, **roleplay actions** wrapped in asterisks (`*she looks up from the charts*`) render as muted-italic secondary text — a Character.AI signature that makes narration feel different from speech.

The single brand accent is a calm, confident **blue** (`#3A7BFD`). It is rationed hard: it appears on the **send button** (a filled circle in the composer), primary CTAs ("Start chat", "Create a Character"), links, and the active tab — and **never** as a bubble fill. A secondary **lilac** (`#9D7BFF`) appears only as an alternate avatar gradient, giving characters subtle visual variety without introducing a second UI color. Supporting surfaces are the **Discover** grid of character cards (avatar + name + short description + chat-count), the **typing indicator** (three dimming dots in an AI-shaped bubble), and the **persona/creation** flow.

Typography is a geometric, slightly futuristic sans for identity — **Sora** (its open, technical letterforms feel right for an AI product) — used for the wordmark, screen titles, character names, and buttons; paired with **Inter** for message text and descriptions, where reading comfort over long conversations is paramount. Weights stay in the 400/500/600/700 band. Geometry is soft and quiet: bubbles at 18pt (with the 4pt tuck), cards at 16pt, avatars are circles, buttons are full pills. The overall feeling is focused, immersive, intelligent, and unobtrusive — a dark room where the only thing lit is the conversation.

**Key Characteristics:**
- Near-black canvas (`#0F0F10`) — dark-first; nothing competes with the conversation
- The chat IS the product — circular character avatar + centered greeting + upward message stream
- Asymmetric bubbles — AI vs user differentiated by a 4pt tucked corner, NOT by color
- AI bubble `#1C1C1F` / user bubble `#26262A` — deliberately close greys, calm and book-like
- Italic `*roleplay actions*` rendered as muted secondary text — a Character.AI signature
- One rationed accent blue `#3A7BFD` — send button, CTAs, links, active tab; never a bubble
- Lilac `#9D7BFF` as an alternate avatar gradient only — variety without a second UI color
- Character greeting — the first AI message is the persona's in-character hello
- Typing indicator — three dimming dots in an AI-shaped (4pt-tuck) bubble
- Discover grid — character cards: avatar + name + one-line description + chat-count
- Sora (wordmark/titles/names/buttons) + Inter (message text/descriptions)
- Soft quiet geometry — 18pt bubbles, 16pt cards, circular avatars, 500pt pill buttons

## 2. Color Palette & Roles

### Primary (Interactive)
- **Accent Blue** (`#3A7BFD`): The single brand accent — send button, primary CTAs, links, active tab, selection. Rationed; never a bubble fill.
- **Accent Pressed** (`#2E63D6`): Pressed state of blue surfaces.
- **Accent Soft** (`#1B2A4A`): Tinted secondary-button background (text `#9FC0FF`).
- **Lilac** (`#9D7BFF`): Alternate character-avatar gradient end; not a UI control color.

### Canvas & Surfaces (Dark — primary)
- **Canvas** (`#0F0F10`): Primary dark canvas — near-black neutral, not pure black.
- **Surface 1** (`#161618`): Sheets, settings rows, elevated panels.
- **AI Bubble** (`#1C1C1F`): Assistant message bubble fill, typing-indicator bubble.
- **User Bubble** (`#26262A`): User message bubble fill, also Surface 3 / pressed.
- **Surface 2** (`#1E1E21`): Composer field, character-card background.
- **Divider** (`#2A2A2E`): Hairline dividers, bubble borders, header underline.

### Light Mode
Character.AI is dark-first. A light theme exists; pair these (the accent stays identical).

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0F0F10` |
| Surface 1 | `#F4F4F6` | `#161618` |
| AI Bubble | `#F1F1F3` | `#1C1C1F` |
| User Bubble | `#E4E8F2` | `#26262A` |
| Divider | `#E6E6EA` | `#2A2A2E` |
| Text Primary | `#16161A` | `#ECECEE` |
| Text Secondary | `#66666E` | `#9A9AA2` |
| Accent Blue | `#3A7BFD` | `#3A7BFD` |
| Lilac | `#9D7BFF` | `#9D7BFF` |

### Text
- **Text Primary** (`#ECECEE` dark / `#16161A` light): Message text, names, primary content.
- **Text Secondary** (`#9A9AA2` dark / `#66666E` light): Descriptions, italic roleplay actions, metadata.
- **Text Tertiary** (`#66666E` dark / `#9A9AA6` light): Timestamps, "by @creator", placeholders, typing dots.
- **On-Accent** (`#FFFFFF`): Text/icon on blue fills.
- **Link** (`#3A7BFD`): Inline links and "@creator" mentions when tappable.

### Semantic
- **Success** (`#4ED99A`): "Character saved", positive confirmation.
- **Error** (`#FF6B6B`): Generation error, message-failed retry, form errors.
- **Warning** (`#FFB454`): Content/safety notices (used sparingly, never near bubbles as accent).
- **Selection** (`rgba(58,123,253,0.20)`): Highlighted/selected text or row.

## 3. Typography Rules

### Font Family
- **Wordmark / Titles / Names / Buttons**: `Sora` (by Jonathan Barnbrook / Soulcraft, Google Fonts, SIL OFL) — a geometric sans with open counters and a subtly technical character that suits an AI product; approximates Character.AI's clean modern brand type.
- **Message Text / Descriptions / Body**: `Inter` (by Rasmus Andersson, SIL OFL) — engineered for screen legibility over long reading sessions; correct for multi-paragraph AI replies and bios.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- Character.AI's production app uses a clean geometric/system pairing; **Sora + Inter** is the closest free, well-hinted match.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Sora | 32pt | 700 | 1.2 | -0.5pt | Onboarding / empty-state hero |
| Screen Title | Sora | 24pt | 600 | 1.25 | -0.3pt | "Discover", "Create" |
| Greeting Name | Sora | 20pt | 600 | 1.3 | -0.2pt | Character name above the greeting |
| Header Name | Sora | 17pt | 600 | 1.3 | -0.1pt | Character name in chat header |
| Card Name | Sora | 15pt | 600 | 1.3 | 0pt | Character name on Discover card |
| Message | Inter | 15pt | 400 | 1.5 | 0pt | AI / user bubble text (primary) |
| Bubble (Dense) | Inter | 14pt | 400 | 1.5 | 0pt | Compact bubble text variant |
| Roleplay Action | Inter | 15pt | 400 *italic* | 1.5 | 0pt | `*action*` — color Text Secondary |
| Card Description | Inter | 13pt | 400 | 1.45 | 0pt | One-line character description |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Persona editor, settings copy |
| Meta | Inter | 11pt | 500 | 1.3 | 0.1pt | "by @lumen · 2.4M chats", timestamps |
| Button | Sora | 15pt | 600 | 1.0 | 0pt | Primary / soft / outline CTA |
| Tab Label | Sora | 10pt | 600 | 1.0 | 0.2pt | Bottom tab labels |

### Principles
- **Two-font system**: Sora carries identity (wordmark, titles, names, buttons); Inter carries reading comfort (all message text, descriptions, long body). Never set message text in Sora.
- **Weights 400/500/600/700 only** — no thin, no black.
- **Roleplay actions are italic + secondary**: any `*…*` span renders Inter 15pt *italic* in Text Secondary `#9A9AA2`. This is a load-bearing Character.AI signature — it separates narration from speech.
- **Generous message line-height (1.5)**: AI replies are long; comfortable leading prevents fatigue.
- **Quiet meta**: "by @creator · N chats" stays at 11pt Text Tertiary — present, never competing with the name.
- **Dynamic Type**: display, titles, names, message text, body scale; tab labels and meta stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Start chat / Send / Continue)**
- Shape: Full pill, 500pt corner radius (send = 40pt circle)
- Background: `#3A7BFD` (Accent Blue)
- Text/Icon: `#FFFFFF`
- Padding: 13pt vertical, 26pt horizontal
- Font: Sora 15pt weight 600
- Pressed: background `#2E63D6` + scale 0.98

**Soft Button (Create a Character / secondary CTA)**
- Background: `#1B2A4A` (Accent Soft tint)
- Text: `#9FC0FF`
- Shape: pill 500pt
- Padding: 13pt / 26pt
- Pressed: background `#22315A`

**Outline Button (Edit persona / tertiary)**
- Background: transparent
- Text: `#ECECEE`
- Border: 1pt `#2A2A2E`
- Shape: pill 500pt
- Padding: 12pt / 22pt
- Pressed: background `#1E1E21`

**Text Button (New chat / inline action)**
- Background: none
- Text: `#3A7BFD`, Sora 14pt weight 600
- No underline
- Pressed: opacity 0.7

**Send Button (composer)**
- 40pt circle, `#3A7BFD`, up-arrow glyph 18pt `#FFFFFF`, stroke 2.4pt
- Disabled (empty field): `#26262A` fill, `#66666E` glyph
- Pressed: `#2E63D6` + scale 0.94

### Chat Bubble (the core atom)

**AI Bubble**
- Background: `#1C1C1F`
- Border: 1pt `#2A2A2E`
- Text: Inter 15pt `#ECECEE`, line-height 1.5
- Corner radius: **4pt top-left**, 18pt top-right, 18pt bottom-right, 18pt bottom-left (the 4pt tuck points back at the avatar)
- Padding: 11pt vertical, 14pt horizontal
- Max width: 86% of content width
- Leading: 26pt circular character avatar, 9pt gap, top-aligned
- Roleplay spans (`*…*`): Inter 15pt *italic* `#9A9AA2`

**User Bubble**
- Background: `#26262A`
- Border: none
- Text: Inter 15pt `#ECECEE`
- Corner radius: 18pt top-left, **4pt top-right**, 18pt bottom-right, 18pt bottom-left (mirrored tuck)
- Padding: 11pt / 14pt
- Aligned to trailing edge; no avatar
- Max width: 86%

**Typing Indicator**
- An AI-shaped bubble (`#1C1C1F`, 1pt `#2A2A2E`, 4pt-tuck) with three 6pt dots `#66666E`
- Dots animate opacity 1 → 0.4 in sequence (see Motion)

**Message Group**
- 12pt vertical gap between messages
- Consecutive same-speaker messages: 4pt gap, avatar shown only on the first

### Character Avatar

- Circular, radial gradient
- Default (blue): `radial-gradient(circle at 36% 30%, #6FA0FF, #3A7BFD 55%, #274FB0)`
- Alternate (lilac): `radial-gradient(circle at 36% 30%, #C7A8FF, #9D7BFF 55%, #6A47C9)`
- Sizes: 64pt (greeting), 40pt (chat header), 48pt (Discover card), 26pt (per-message)
- Real character art (when present) fills the circle; gradient is the fallback/placeholder
- The single warm focal point on an otherwise neutral screen

### Character Card (Discover)

- Shape: rounded rect, 16pt corner radius
- Background: `#1E1E21`, 1pt `#2A2A2E` border
- Padding: 16pt; internal gap 8pt
- Contents (vertical): 48pt avatar · name (Sora 15pt w600 `#ECECEE`) · description (Inter 13pt `#9A9AA2`, 2 lines max) · footer row (chat-bubble glyph 11pt + "2.4M chats" Inter 11pt `#66666E`)
- Grid: 2-up on phone, 12pt gap
- Pressed: background `#26262A` + scale 0.99

### Navigation

**Chat Header**
- Height: 48pt + safe area
- Leading: back chevron 18pt `#ECECEE`
- 40pt character avatar + name (Sora 17pt w600) + sub ("by @lumen · 2.4M chats" Inter 11pt `#9A9AA2`)
- Trailing: ⋮ overflow (new chat, persona, restart, share) 18pt `#9A9AA2`
- Underline: 0.5pt `#2A2A2E`
- Background: canvas (no blur needed; chat scrolls under cleanly)

**Bottom Tab Bar**
- Height: 60pt + safe area
- Background: `#0F0F10` with 0.5pt top border `#2A2A2E`
- Tabs (4): Discover · Chats · Create · Profile
- Icon: 23pt
- Active: icon + label `#3A7BFD` Sora 10pt w600
- Inactive: `#66666E`
- No tint pill — accent color is the active signal

### Input Fields

**Composer**
- Height: 44pt pill, `#1E1E21`, 1pt `#2A2A2E`
- Placeholder: "Message {Character}…" Inter 14pt `#66666E`
- Padding: 0 / 16pt; multiline expands up to ~5 lines then scrolls
- Trailing: 40pt blue send circle (disabled grey when empty)
- Focus: 1pt `#3A7BFD` border

**Persona / Search Field**
- Height: 44pt, `#1E1E21`, 12pt radius (search) or auto-grow (persona editor)
- Leading magnifier 16pt `#9A9AA2`; placeholder Inter 14pt `#66666E`

### Distinctive Components

**Asymmetric Tuck Bubble** — the signature: speaker identity comes from a single 4pt corner, not color.

**Roleplay Action Styling** — `*…*` rendered italic + secondary; narration visually distinct from speech.

**Character Greeting Header** — every chat opens with avatar + name + one-line description + the persona's in-character first message.

**Typing Indicator** — three sequentially-dimming dots inside an AI-shaped bubble.

**Character Card** — the Discover unit: avatar, name, one-line hook, chat-count social proof.

**Persona / Definition Editor** — long-form auto-grow text area where users author a character's voice (Sora label, Inter body, accent-blue save).

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 9, 11, 12, 14, 16, 18, 24, 32, 40, 48, 64
- Message-to-message gap: 12pt (4pt within a same-speaker group)
- Bubble padding: 11pt vertical / 14pt horizontal
- Screen side inset: 14–16pt

### Grid & Container
- iPhone: single chat column, 14pt side insets; Discover = 2-up card grid (12pt gap)
- Bubble max-width: 86% of content width
- iPad: chat max-width 720pt centered; Discover = 3–4-up grid
- No multi-column chat — the conversation is a single focused stream

### Whitespace Philosophy
- **The conversation owns the screen**: chrome is minimal (thin header, thin tab bar) so the message stream dominates
- **Calm bubble rhythm**: 12pt between messages, generous 1.5 line-height — long AI replies stay readable
- **The avatar is the only warmth**: one gradient circle per screen; everything else is neutral grey
- **Greeting centered, dialogue left/right**: the opening is a centered "title card"; the conversation then flows asymmetrically

### Border Radius Scale
- Square (0pt): full-bleed dividers
- Tuck (4pt): the speaker-side bubble corner; small chips
- Subtle (8pt): inline tags, small affordances
- Standard (12pt): search field, sheet sub-elements
- Card (16pt): Discover character cards
- Bubble (18pt): the three non-tucked bubble corners
- Pill (500pt): all buttons, composer field
- Circle (50%): avatars, send button, tab hit areas

## 6. Depth & Elevation

Character.AI is flat and quiet — elevation is barely-there; the conversation, not shadow, carries focus.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, bubbles, header, tab bar |
| Bubble (Level 0.5) | 1pt `#2A2A2E` border (AI only) | AI bubble + typing bubble edge definition |
| Card (Level 1) | 1pt `#2A2A2E` border (dark) / `0 1px 2px rgba(0,0,0,0.05)` (light) | Discover cards |
| Sheet (Level 2) | `0 -8px 32px rgba(0,0,0,0.5)` | Persona editor, settings, share sheets |
| Scrim | `rgba(0,0,0,0.5)` | Dim behind modals and bottom sheets |

**Shadow Philosophy**: Character.AI uses essentially no drop shadows. The AI bubble's 1pt border is the only "edge" treatment — it separates the assistant's words from the near-black canvas without adding visual noise. User bubbles need no border (the lighter grey `#26262A` lifts off the canvas on its own). Sheets get a soft upward shadow only so they read as dismissible layers. The design philosophy is *flat, focused, and out of the way* — depth would distract from the conversation.

### Motion
- **Message send**: user bubble appears with a 120ms fade + 8pt upward slide; list auto-scrolls to bottom 250ms ease-out
- **AI streaming**: text streams token-by-token into the AI bubble; the bubble height grows smoothly (no layout jump); a subtle caret/cursor blinks at the tail while streaming
- **Typing indicator**: three dots each animate opacity 1 → 0.4 → 1 on a 1.2s loop, staggered 0.16s apart
- **Greeting entrance**: avatar fades+scales (0.92 → 1.0) 260ms ease-out, then name and description fade in 120ms later; the greeting bubble slides up 200ms
- **Bubble tap (long-press)**: scale 0.98 + reveal context actions (Copy, Retry, Rate) in a 150ms fade
- **Tab switch**: icon+label tint cross-dissolves to blue 180ms; no pill slide
- **Card press**: scale 0.99 + 80ms ease-out
- **Sheet present**: bottom sheet slides up 320ms ease-out; scrim fades in parallel
- **Stop generation**: send button morphs to a stop square 150ms while streaming; reverts on completion
- **Haptics**: light selection on send, soft tick on tab switch, light impact on long-press actions; none during streaming (it would buzz continuously)

## 7. Do's and Don'ts

### Do
- Make the chat the centerpiece — circular avatar, centered greeting, upward message stream on a near-black canvas
- Differentiate AI vs user by the 4pt tucked corner, NOT by color
- Keep AI `#1C1C1F` and user `#26262A` as close, calm greys
- Render `*roleplay actions*` as muted-italic secondary text — it's a load-bearing signature
- Ration accent blue `#3A7BFD` to the send button, CTAs, links, and active tab — never a bubble
- Use the character avatar gradient (blue or lilac) as the single warm focal point per screen
- Open every chat with the persona's in-character greeting
- Show the typing indicator as three dimming dots in an AI-shaped bubble
- Use Sora for identity (titles, names, buttons), Inter for all message text
- Stream AI replies token-by-token with smooth height growth and a blinking tail caret
- Keep chrome minimal — thin header, thin tab bar

### Don't
- Don't color-code speakers with a bright "user blue" bubble — the tuck does the work
- Don't use the accent blue as any bubble fill (it breaks the calm reading surface)
- Don't render roleplay `*actions*` as plain body text — they must be italic + secondary
- Don't set message text in Sora — Inter carries long-read comfort
- Don't add drop shadows to bubbles — the AI 1pt border is the only edge treatment
- Don't tint the whole active tab (pill/label block) — the accent color is enough
- Don't make the character description louder than the name on cards
- Don't use pure black for the canvas — `#0F0F10` near-black keeps bubbles legible
- Don't buzz haptics during streaming — it would be continuous and annoying
- Don't introduce a second UI accent — lilac is an avatar gradient only, not a control color
- Don't crowd the greeting — it's a centered, breathing "title card" before dialogue begins

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max-width 88%; greeting avatar 56pt; Discover stays 2-up tighter gap |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island; header starts below island |
| iPhone 15/16 Pro Max | 430pt | Greeting avatar 72pt; message text may step to 16pt; bubble max 84% |
| iPad (portrait) | 768pt | Chat max-width 720pt centered; Discover 3-up grid |
| iPad (landscape) | 1024pt+ | Split: chat list / conversation; Discover 4-up |

### Dynamic Type
- Display, titles, greeting/header/card names, message text, body: full scale
- Tab labels, "by @creator · N chats" meta, typing dots: FIXED (layout-sensitive)
- Message text scales generously; bubble grows with it (max-width % constant)

### Orientation
- Chat supports rotation; bubbles reflow to the wider max-width
- iPad landscape uses a list + conversation split
- Persona editor rotates freely

### Touch Targets
- Send button: 40pt circle (≥ 44pt hit via padding)
- Bubble long-press: full bubble is the target
- Tab icon: 23pt glyph, 48pt hit
- Header back / overflow: 30pt glyph, 44pt hit
- Character card: full card tappable (→ chat)

### Safe Area Handling
- Top: header respects Dynamic Island / notch
- Bottom: composer + home indicator respected; messages scroll clear of the composer
- Keyboard: composer rises with keyboard; message list scrolls above it; greeting stays scrolled out cleanly
- Sides: 14–16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0F0F10` dark / `#FFFFFF` light (near-black, not pure black)
- Surface 1 / 2: `#161618` / `#1E1E21`
- AI bubble / user bubble: `#1C1C1F` / `#26262A`
- Divider / bubble border: `#2A2A2E`
- Text primary / secondary / tertiary: `#ECECEE` / `#9A9AA2` / `#66666E`
- Accent blue: `#3A7BFD` (pressed `#2E63D6`, soft `#1B2A4A`)
- Lilac (avatar gradient only): `#9D7BFF`
- On-accent text: `#FFFFFF`
- Success / Error: `#4ED99A` / `#FF6B6B`

### Example Component Prompts
- "Build the Character.AI chat screen in SwiftUI: near-black `#0F0F10` canvas. Header (48pt): back chevron, 40pt circular avatar with `radial-gradient(circle at 36% 30%, #6FA0FF, #3A7BFD 55%, #274FB0)`, name Sora 17pt w600 `#ECECEE`, sub 'by @lumen · 2.4M chats' Inter 11pt `#9A9AA2`, trailing ⋮. Greeting block (centered): 64pt avatar, name Sora 20pt w600, one-line description Inter 13pt `#66666E`. Then the message stream with 12pt gaps."

- "Create the Character.AI AI bubble: background `#1C1C1F`, 1pt `#2A2A2E` border, text Inter 15pt `#ECECEE` line-height 1.5, padding 11/14pt, max-width 86%. Corner radii: top-left 4pt, the other three 18pt (the 4pt tuck points back at a 26pt leading circular avatar with 9pt gap). Any `*…*` span renders Inter 15pt italic `#9A9AA2`."

- "Create the Character.AI user bubble: background `#26262A`, no border, text Inter 15pt `#ECECEE`, padding 11/14pt, max-width 86%, trailing-aligned, no avatar. Corner radii: top-right 4pt, the other three 18pt (mirrored tuck). Do NOT use the accent blue as the fill."

- "Build the Character.AI typing indicator: an AI-shaped bubble (`#1C1C1F`, 1pt `#2A2A2E`, top-left 4pt / others 18pt) containing three 6pt `#66666E` dots; each dot animates opacity 1 → 0.4 → 1 on a 1.2s loop, staggered 0.16s apart."

- "Render a Character.AI Discover card: 16pt radius, `#1E1E21` bg, 1pt `#2A2A2E` border, 16pt padding, 8pt internal gap. Vertical: 48pt circular avatar (blue or lilac radial gradient), name Sora 15pt w600 `#ECECEE`, description Inter 13pt `#9A9AA2` (2 lines max), footer = chat-bubble glyph 11pt + '2.4M chats' Inter 11pt `#66666E`. 2-up grid, 12pt gap. Press: scale 0.99 + bg `#26262A`."

- "Build the Character.AI composer: 44pt pill `#1E1E21` + 1pt `#2A2A2E`, placeholder 'Message Aria…' Inter 14pt `#66666E`, auto-grow to ~5 lines. Trailing 40pt circle send button: `#3A7BFD` with white up-arrow when text present, `#26262A`/`#66666E` when empty. While streaming, morph the send circle to a stop square over 150ms."

### Iteration Guide
1. The chat is the product — near-black `#0F0F10` canvas, circular avatar, centered greeting, upward stream
2. AI vs user is the 4pt tucked corner, NOT color — keep `#1C1C1F` and `#26262A` close and calm
3. `*Roleplay actions*` MUST render italic + secondary `#9A9AA2` — load-bearing signature
4. Accent blue `#3A7BFD` is rationed: send button, CTAs, links, active tab — never a bubble fill
5. The avatar gradient (blue default, lilac alt) is the only warmth — lilac is NOT a UI control color
6. Every chat opens with the persona's in-character greeting under a centered name + description
7. Type is two-font: Sora for identity (titles/names/buttons), Inter for all message text/descriptions
8. Stream AI replies token-by-token with smooth height growth + a blinking tail caret; no haptics while streaming
9. Chrome is minimal — thin header, thin tab bar; no drop shadows (AI 1pt border is the only edge)
10. Canvas is near-black `#0F0F10`, not pure black; bubbles read calmly against it
