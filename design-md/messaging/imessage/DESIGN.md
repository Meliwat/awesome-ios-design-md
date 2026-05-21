# Design System Inspiration of iMessage (iOS)

## 1. Visual Theme & Atmosphere

iMessage is the canonical iOS conversation surface — and arguably the most-imitated chat UI on the planet. Its design philosophy is "the message is the moment": the interface is engineered to disappear so the bubble, the tapback, and the typing dots carry the entire emotional weight of a conversation. Open a thread and you see a clean white canvas (`#FFFFFF` light / true-black `#000000` dark), a hairline-thin navigation bar with a circular contact avatar centered above the contact's name, a vertically-scrolling stack of rounded "bubbles," and a blur-backed input bar pinned to the bottom with a `+` attachment button, a pill text field, and a circular send button. There are no cards, no list dividers inside the thread, no chrome competing with the words.

The single most recognizable element is the **bubble pair**: outgoing iMessages render in iMessage Blue (`#007AFF`) with white text and a tail on the bottom-right; incoming bubbles render in a neutral gray (`#E9E9EB` light / `#26262A` dark) with primary-color text and a tail on the bottom-left. When a message can't be sent over iMessage (recipient not on Apple, no data), the outgoing bubble falls back to SMS Green (`#34C759`) — this blue-vs-green distinction is so culturally loaded it has its own discourse. Bubbles use a ~19pt corner radius with the corner nearest the sender's edge "pinched" to ~6pt to form the tail. Consecutive messages from the same sender group with ~3pt gaps; a sender change inserts ~9pt.

The second signature moment is the **tapback**: long-press any bubble and a floating reaction strip appears with six glyphs — Heart, Thumbs Up, Thumbs Down, Ha-Ha, Double-Exclamation, Question Mark. The chosen reaction docks as a small chip overlapping the top edge of the bubble. Add **typing dots** (three pulsing gray circles in an incoming-shaped bubble), **delivery receipts** ("Delivered" / "Read 9:41 AM" in tiny gray right-aligned text under the last outgoing bubble), inline message effects (Slam, Loud, Gentle, Invisible Ink), and full-bleed emoji, and you have the complete iMessage emotional vocabulary.

Color is restrained and almost entirely systemic. Beyond the two brand bubble colors, iMessage leans on Apple's system palette: `systemBlue` for every interactive control and link, `systemGray` ramps for bubbles and secondary text, `systemRed` for FaceTime/decline and destructive actions, `systemGreen` for SMS. There is no decorative brand palette — the blue/green bubble dichotomy *is* the brand. Typography is pure San Francisco (SF Pro Text / SF Pro Display), Apple's system face, used at Dynamic Type sizes with the message body at 16-17pt regular and the contact headline at 17pt semibold.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / true-black (`#000000`) dark — minimum chrome, maximum message
- Blue/gray bubble pair: outgoing iMessage Blue (`#007AFF`), incoming gray (`#E9E9EB` / `#26262A`)
- SMS Green (`#34C759`) fallback bubble when iMessage is unavailable
- ~19pt rounded bubbles with a pinched ~6pt tail corner on the sender's edge
- Tapback reaction strip — Heart / Thumbs Up / Thumbs Down / Ha-Ha / ‼️ / ❓ — docks as an overlapping chip
- Typing indicator — three pulsing gray dots in an incoming-shaped bubble
- Delivery receipts — "Delivered" / "Read 9:41 AM" tiny gray right-aligned under last outgoing message
- Blur-backed input bar — `+` attach, pill text field, circular send (arrow-up) button
- SF Pro typography at Dynamic Type sizes; no custom brand font
- System-color UI — `systemBlue` controls, `systemGray` ramps, `systemRed` FaceTime/destructive
- Message effects (Slam / Loud / Gentle / Invisible Ink) and screen effects
- Centered nav with circular avatar above contact name + chevron-down disclosure

## 2. Color Palette & Roles

### Primary (Interactive)
- **iMessage Blue** (`#007AFF`): Outgoing iMessage bubble, every interactive control, links, send button — `systemBlue`.
- **iMessage Blue Pressed** (`#0062CC`): Pressed/highlighted state of blue controls.
- **SMS Green** (`#34C759`): Outgoing SMS/MMS fallback bubble, "Send as Text Message" — `systemGreen`.
- **SMS Green Pressed** (`#248A3D`): Pressed state of green bubble/control.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Thread background, primary light canvas.
- **Incoming Bubble** (`#E9E9EB`): Gray incoming message bubble — `systemGray5`-ish.
- **Surface Gray** (`#F2F2F7`): Grouped table background (conversation list), settings — `systemGroupedBackground`.
- **Input Field** (`#FFFFFF` with `#C6C6C8` 1pt border): Pill compose field.
- **Divider** (`#3C3C434A`): Hairline nav separator (`separator`, ~29% opacity).

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#000000`): True black thread background (OLED).
- **Incoming Bubble (Dark)** (`#26262A`): Dark gray incoming bubble.
- **Dark Surface 1** (`#1C1C1E`): Elevated surfaces, conversation list rows — `systemGray6` dark.
- **Dark Surface 2** (`#2C2C2E`): `+` button background, tapback strip, attachment tray — `systemGray5` dark.
- **Dark Divider** (`#38383A`): Hairline nav/list separators (`separator` dark).

### Text
- **Text Primary Light** (`#000000`): Incoming bubble text, primary labels — `label`.
- **Text on Blue/Green** (`#FFFFFF`): Outgoing bubble text — always white on blue and green.
- **Text Secondary** (`#3C3C4399` light / `#EBEBF599` dark): Timestamps, contact subtitle — `secondaryLabel`.
- **Text Tertiary** (`#3C3C434D` light / `#EBEBF54D` dark): "Delivered", placeholder, typing dots — `tertiaryLabel`.
- **Text Primary Dark** (`#FFFFFF`): Incoming bubble text and labels on dark.

### Semantic
- **Error / FaceTime Red** (`#FF3B30` light / `#FF453A` dark): Decline call, delete message, unsend confirmation — `systemRed`.
- **Success Green** (`#34C759` / `#30D158`): SMS sent confirmation, accept call — `systemGreen`.
- **Link Blue** (`#007AFF` / `#2997FF`): Inline links inside bubbles, "Tap to Load" — `systemBlue`.
- **Selection Highlight** (`#007AFF` @ 20%): Text selection within a bubble.

### Bubble Color Matrix

| State | Light Mode | Dark Mode | Text |
|-------|-----------|-----------|------|
| Outgoing iMessage | `#007AFF` | `#007AFF` | `#FFFFFF` |
| Outgoing SMS/MMS | `#34C759` | `#34C759` | `#FFFFFF` |
| Incoming | `#E9E9EB` | `#26262A` | `#000000` / `#FFFFFF` |
| Failed (not delivered) | `#FF3B30` glyph + gray bubble | `#FF453A` glyph + `#26262A` | `#000000` / `#FFFFFF` |
| Tapback chip bg | `#FFFFFF` w/ shadow | `#2C2C2E` | n/a |

## 3. Typography Rules

### Font Family
- **System**: `SF Pro Text` (≤ 19pt) / `SF Pro Display` (≥ 20pt) — Apple's San Francisco. Selected automatically by the OS via the system font; never bundled or named in code (use `.system`).
- **Fallback Stack (web/cross-platform mirror)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: SF proportional by default; tabular figures in timestamps.
- **No custom brand typeface** — iMessage is 100% system San Francisco at Dynamic Type sizes.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 34pt | 700 | 1.2 | 0.4pt | "Messages" list screen title |
| Title 1 | SF Pro Display | 28pt | 700 | 1.2 | 0.36pt | "New Message" sheet title |
| Title 3 | SF Pro Display | 20pt | 600 | 1.25 | 0.38pt | Group name, large contact name |
| Headline | SF Pro Text | 17pt | 600 | 1.3 | -0.4pt | Contact name in chat nav bar |
| Body / Bubble | SF Pro Text | 17pt | 400 | 1.3 | -0.4pt | Message text (Dynamic Type default) |
| Message (compact) | SF Pro Text | 15pt | 400 | 1.3 | -0.2pt | Dense / smaller Dynamic Type |
| Callout | SF Pro Text | 16pt | 400 | 1.3 | -0.3pt | Inline link text, "Tap to Load" |
| Subhead | SF Pro Text | 15pt | 400 | 1.35 | -0.2pt | Conversation list preview line |
| Footnote | SF Pro Text | 13pt | 600 | 1.35 | -0.08pt | "Delivered" / "Read 9:41 AM" |
| Caption 1 | SF Pro Text | 12pt | 400 | 1.35 | 0pt | Timestamp separators |
| Caption 2 | SF Pro Text | 11pt | 600 | 1.35 | 0.06pt | "iMessage" / "SMS" label above thread |
| Tab / Toolbar | SF Pro Text | 11pt | 500 | 1.0 | 0.07pt | Toolbar labels |

### Principles
- **Dynamic Type is mandatory**: Message body, headline, and list previews scale across the user's text-size setting; only timestamps and chrome stay fixed.
- **Weight concentrated at 400 / 500 / 600 / 700**: Regular body, medium toolbar, semibold headline/footnote, bold titles. No thin or black weights.
- **Tail does not change type**: bubble tail/grouping is a shape concern; text metrics never change with bubble position.
- **White text on blue and green is non-negotiable**: outgoing bubbles always use `#FFFFFF` text regardless of mode.
- **Tracking is negative for body** (SF optical), positive for tiny captions — follow Apple's Dynamic Type tracking table.

## 4. Component Stylings

### Buttons

**Send Button (circular)**
- Shape: 30pt circle (icon arrow-up `arrow.up`)
- Background: `#007AFF` (iMessage) / `#34C759` (SMS) / `#8E8E93` disabled when field empty
- Icon: `#FFFFFF`, 15pt, weight semibold
- Pressed: scale 0.92 + slight darken (`#0062CC`)
- Sits at the trailing edge of the input bar

**Primary Action Button (sheets / alerts)**
- Shape: Rounded rectangle, 14pt corner radius (or full-width 12pt in sheets)
- Background: `#007AFF`
- Text: `#FFFFFF`, SF Pro 17pt weight 600
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `#0062CC` + opacity 0.85

**Tinted Button (secondary)**
- Background: `#007AFF` @ 16% opacity (`rgba(0,122,255,0.16)`)
- Text: `#007AFF`, SF Pro 15pt weight 600
- Corner radius: 14pt
- Used for "Tapback", in-thread quick actions

**Text Button (nav / toolbar)**
- Background: transparent
- Text: `#007AFF`, SF Pro 17pt weight 400 ("Edit", "Cancel", "Done")
- Pressed: opacity 0.3

**`+` Attachment Button**
- Size: 30pt circle, background `#E9E9EB` light / `#2C2C2E` dark
- Icon: `plus` 16pt, `#8E8E93` / `secondaryLabel`
- Tap expands the iMessage app/attachment tray

### Message Bubble (the core atom)

The bubble is iMessage's fundamental unit.

**Outgoing iMessage Bubble**
- Background: `#007AFF`
- Text: `#FFFFFF`, SF Pro 17pt w400
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 19pt, with bottom-right corner at 6pt (tail) when last in a group
- Alignment: trailing (right), max width ~78% of thread
- Shadow: none

**Outgoing SMS Bubble** — identical geometry, background `#34C759`.

**Incoming Bubble**
- Background: `#E9E9EB` light / `#26262A` dark
- Text: `#000000` light / `#FFFFFF` dark, SF Pro 17pt w400
- Corner radius: 19pt, bottom-left corner at 6pt (tail) when last in a group
- Alignment: leading (left)

**Grouping rules**
- Same sender consecutive: 3pt vertical gap, only the last bubble in the run shows the tail
- Sender change: 9pt vertical gap, new bubble shows the tail
- Day/time separator: centered 12pt caption with `secondaryLabel`, 10pt top / 6pt bottom margin

**Bubble States**
- Sending: bubble at 50% opacity until server ack
- Failed: red `exclamationmark.circle.fill` (`#FF3B30`) to the left of the bubble; tap to retry
- With tapback: reaction chip overlaps the top corner nearest the bubble's owner (-16pt y offset)
- Edited (iOS 16+): "Edited" in 11pt `tertiaryLabel` below the bubble
- With effect: bubble animates in (Slam = scale 1.3→1, Loud = scale + shake, Gentle = scale 0.5→1)

### Tapback Reaction Strip

- Floating capsule, background `#FFFFFF` w/ `rgba(0,0,0,0.12) 0 6px 20px` shadow (light) / `#2C2C2E` (dark)
- Corner radius: 24pt (capsule)
- Six 28pt glyphs: ❤️ Heart, 👍 Thumbs Up, 👎 Thumbs Down, 😂 Ha-Ha, ‼️ Emphasize, ❓ Question
- Appears on long-press (~500ms) anchored above the pressed bubble with a scale-up + spring
- Selected reaction docks as a 16pt chip overlapping the bubble's top-near corner; chip bg `#FFFFFF`/`#2C2C2E` with subtle border

### Typing Indicator

- Incoming-shaped bubble (gray, leading, tail) containing three 7pt circles
- Circle color: `tertiaryLabel` (`#3C3C434D` / `#EBEBF54D`)
- Animation: each dot scales 0.6 → 1.0 → 0.6 with a 0.2s stagger, ~1.4s loop, ease-in-out
- Appears at the bottom of the thread when the other party is composing

### Delivery Receipt

- Right-aligned, under the last outgoing bubble
- Text: "Delivered" or "Read 9:41 AM" — SF Pro 13pt w600 `secondaryLabel`
- 2pt top margin from bubble; collapses when superseded by a newer outgoing message
- "Read" appears only if the recipient has read receipts enabled

### Navigation

**Chat Nav Bar**
- Height: 44pt + safe area, hairline `separator` bottom border
- Leading: back chevron (`chevron.left` 17pt `#007AFF`) + unread count badge
- Center: circular contact avatar (34pt) stacked above contact name (SF Pro 13pt w600) + `chevron.down` 9pt disclosure (opens contact card)
- Trailing: FaceTime icon (`video` 22pt `#007AFF`)
- Material: `.ultraThinMaterial` blur when content scrolls under it

**Conversation List (Messages home)**
- Large title "Messages" (34pt w700), search bar below, "Edit" / compose (`square.and.pencil`) in nav
- Each row: 60pt tall — leading 50pt circular avatar, title (contact, 17pt w600), preview (15pt `secondaryLabel`, 2-line truncated), trailing timestamp (13pt `secondaryLabel`) + unread blue dot (10pt `#007AFF`)
- Swipe actions: Pin (orange), Mute (purple/indigo), Delete (red)

### Input Fields

**Compose Bar**
- Height: 36pt field + 8/16pt padding, blur-backed (`.ultraThinMaterial`)
- Leading: `+` 30pt circular attachment button
- Center: pill text field — 18pt corner radius, 1pt `#C6C6C8` / `#38383A` border, transparent fill, placeholder "iMessage" / "Text Message" in `tertiaryLabel`
- Trailing: 30pt circular send button (arrow-up), color = bubble color, disabled gray when empty
- Grows up to ~5 lines then scrolls internally

**Search Bar (list screen)**
- Height: 36pt, background `#E9E9EB` / `#1C1C1E`, 10pt corner radius
- Leading `magnifyingglass` 15pt `tertiaryLabel`, placeholder "Search"

### Distinctive Components

**Tapback Strip + Docked Chip** — see above; the single most copied iMessage interaction.

**Typing Dots** — three pulsing gray circles; signature "they're typing" affordance.

**Message Effect Picker** — long-press send → "Send with effect" sheet with Bubble effects (Slam, Loud, Gentle, Invisible Ink) and Screen effects (Balloons, Confetti, Lasers, Fireworks, etc.); preview animates in place.

**Inline Rich Link Preview** — pasted URL becomes a card inside the bubble flow: leading thumbnail, title (15pt w600), domain (13pt `secondaryLabel`), 13pt corner radius, background matches bubble or `systemBackground`.

**Photo / Sticker Tray** — `+` opens a horizontal app strip (Photos, Stickers, Cash, Music, etc.); recent photos in a grid above the keyboard.

**Read Receipt Line** — "Read 9:41 AM"; quietly authoritative, 13pt gray, right-aligned.

**Day Separator** — centered timestamp with the channel label ("iMessage" / "SMS") above the first bubble of a session.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 2, 4, 8, 12, 14, 16, 20, 24, 32, 44
- Same-sender bubble gap: 3pt
- Sender-change gap: 9pt
- Thread horizontal inset: 14pt (bubbles), bubble max width ~78% of thread width
- Bubble internal padding: 8pt vertical / 14pt horizontal

### Grid & Container
- iPhone: full-width thread, 14pt side gutters for bubbles
- Bubble max width: ~78% of available width (so the opposite side always shows whitespace and sender direction stays unambiguous)
- iPad: split view — conversation list (~320pt) + thread detail; thread content max ~680pt centered
- Input bar: full-width, pinned, safe-area aware

### Whitespace Philosophy
- **Conversation rhythm**: tight 3pt within a sender's run, looser 9pt on sender change — the eye reads "turns" in the dialogue
- **No dividers in-thread**: only day/time separators break the flow; bubbles never get hairlines or cards
- **One-sided emphasis**: the ~78% max width guarantees the empty opposite margin, which *is* the directional cue
- **Chrome recedes**: nav and input bars use system materials and dim; the colored bubbles are the only saturated surfaces

### Border Radius Scale
- Square (0pt): full-bleed image attachments edge-to-edge (rare)
- Tail (6pt): the pinched bubble corner on the sender's edge
- Subtle (10pt): search bar, rich-link card thumbnails
- Standard (14pt): primary buttons, alert/sheet action rows, rich-link cards
- Bubble (19pt): the message bubble body radius
- Comfortable (22pt): attachment tray cells, large pills
- Capsule (500pt): tapback strip, input pill, segmented controls
- Circle (50%): avatars, send button, `+` button, unread dots

## 6. Depth & Elevation

iMessage is nearly flat; depth is conveyed by system materials (blur), not drop shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Bubbles, thread content, list rows |
| Material (Level 1) | `.ultraThinMaterial` / `.regularMaterial` blur | Nav bar + input bar when content scrolls beneath |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 6px 20px` (light) / 1pt border on dark | Tapback strip, context menu, effect picker popover |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -4px 24px` | Attachment tray, share sheet, "send with effect" sheet |
| Modal Overlay | `rgba(0,0,0,0.4)` dim | Behind full-screen effect playback and modals |

**Shadow Philosophy**: iMessage gets its sense of layering from Apple's blur materials, not from elevation shadows on content. The only true shadow lives under the floating tapback/context-menu popover so it visibly hovers above the thread. On dark mode that shadow is nearly invisible, so the popover gains a faint 1pt `separator` border as the elevation cue. Bubbles themselves are always shadowless and flat.

### Motion
- **Bubble send**: outgoing bubble springs up from the input field — scale 0.6 → 1.0, slide up ~40pt, `spring(response: 0.35, damping: 0.75)` + light haptic
- **Tapback present**: long-press (~500ms) → strip scales 0.7 → 1.0 with spring; selecting docks the chip with a 0.2s pop + soft haptic
- **Typing dots**: continuous 1.4s loop, three dots staggered 0.2s, scale 0.6 ↔ 1.0 ease-in-out
- **Delivery receipt**: "Delivered" → "Read" crossfades in 0.2s
- **Message effects**: Slam = scale 1.3 → 1 + screen shake; Loud = scale 1.4 + jitter; Gentle = scale 0.5 → 1 fade; Invisible Ink = particle mask reveal on tap
- **Screen effects**: full-screen overlay (balloons rise, confetti falls, lasers sweep) ~2.5s, then fades
- **Scroll-to-bottom**: new message while scrolled up shows a chevron pill; tap = animated scroll, 0.3s ease-out
- **Nav material**: blur intensifies as content scrolls under the bar (0.2s)
- **Haptics**: light impact on send, soft impact on tapback dock, success haptic on effect trigger

## 7. Do's and Don'ts

### Do
- Use white (`#FFFFFF`) light canvas / true black (`#000000`) dark — black for OLED in dark mode
- Render outgoing iMessages in iMessage Blue `#007AFF`, SMS fallback in Green `#34C759`
- Use neutral gray for incoming bubbles — `#E9E9EB` light / `#26262A` dark — never tinted
- Pinch the sender-edge corner to ~6pt to form the tail; keep the other three at ~19pt
- Group same-sender messages at 3pt, separate sender turns at 9pt
- Always use white text on blue and green bubbles, primary label on gray
- Provide the six-glyph tapback strip on long-press and dock the reaction as an overlapping chip
- Show typing as three pulsing gray dots in an incoming-shaped bubble
- Show "Delivered" / "Read 9:41 AM" as tiny gray right-aligned text under the last outgoing bubble
- Use San Francisco at Dynamic Type sizes — never a custom brand font
- Use system materials (blur) for nav and input bar, not opaque bars
- Keep bubble max width ~78% so the empty opposite margin signals direction

### Don't
- Don't put dividers, cards, or shadows on message bubbles — they're flat and edge-grouped
- Don't tint incoming bubbles — they are neutral gray, the contrast against blue is the point
- Don't swap the blue/green semantics — blue = iMessage, green = SMS, this is culturally fixed
- Don't use a custom font — iMessage is pure system SF Pro at Dynamic Type
- Don't make bubbles full-width — the ~78% cap and one-sided whitespace are the directional cue
- Don't keep the tail on every bubble in a run — only the last bubble of a same-sender group
- Don't use pure black text on a blue bubble — outgoing text is always `#FFFFFF`
- Don't add a brand accent palette — `systemBlue`/`systemGreen`/`systemGray` is the entire system
- Don't animate aggressively outside effects — base motion is quiet spring, 0.2–0.35s
- Don't put a heavy drop shadow on bubbles for "depth" — depth is blur material, content is flat
- Don't show "Read" if the user hasn't enabled read receipts — respect the setting

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max ~290pt; compact nav (no Dynamic Island) |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above nav bar |
| iPhone 15/16 Pro Max | 430pt | Wider bubbles (~335pt), larger avatars |
| iPad (portrait) | 768pt | Split view: list (~320pt) + thread; thread content max ~680pt centered |
| iPad (landscape) | 1024pt+ | Persistent list + thread; effects render in the thread pane |

### Dynamic Type
- Message body, contact headline, list preview: full scale (xSmall → AX5)
- Timestamps, "Delivered/Read", tab/toolbar labels, typing dots: FIXED
- Bubble grows to fit text at the chosen size; max-width % stays constant

### Orientation
- Thread supports rotation; bubbles reflow to the new width keeping the ~78% cap
- iPad keeps split view in both orientations; effects play in the thread column

### Touch Targets
- Send / `+` buttons: 30pt glyph, 44pt hit area
- Bubble: full bubble is the long-press target for tapback (min 44pt tall effective)
- Tapback glyphs: 28pt glyph, 44pt hit
- Nav back / FaceTime: 44pt hit
- List row: full-row tap, 60pt tall

### Safe Area Handling
- Top: nav bar respects safe area + Dynamic Island
- Bottom: input bar respects home indicator; thread scrolls above the keyboard
- Keyboard: thread content insets animate with the keyboard; input bar rides above it
- Sides: 14pt bubble gutters; full-bleed only for edge attachments

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#000000`
- Outgoing iMessage bubble: `#007AFF`
- Outgoing SMS bubble: `#34C759`
- Incoming bubble: `#E9E9EB` light / `#26262A` dark
- Bubble text (outgoing): `#FFFFFF`
- Bubble text (incoming): `#000000` light / `#FFFFFF` dark
- Text secondary: `#3C3C4399` light / `#EBEBF599` dark
- "Delivered/Read" (tertiary): `#3C3C434D` light / `#EBEBF54D` dark
- FaceTime / destructive: `#FF3B30` light / `#FF453A` dark
- Surface 2 (`+` / tapback strip dark): `#2C2C2E`
- Link: `#007AFF` / `#2997FF`

### Example Component Prompts
- "Build an iMessage outgoing bubble in SwiftUI: background `#007AFF`, text `#FFFFFF` SF Pro 17pt regular, padding 8pt vertical / 14pt horizontal, corner radius 19pt with `.bottomTrailing` corner at 6pt (tail) when it's the last bubble in a same-sender run. Right-aligned, max width 78% of the thread. No shadow. SMS variant: identical geometry, background `#34C759`."

- "Create an iMessage incoming bubble: background `#E9E9EB` (light) / `#26262A` (dark), text `label` color SF Pro 17pt regular, corner radius 19pt with `.bottomLeading` at 6pt for the tail, left-aligned, max width 78%. Group consecutive same-sender bubbles with 3pt gaps and only show the tail on the last; insert 9pt on sender change."

- "Render the iMessage tapback strip: a floating capsule (corner radius 24pt) anchored above a long-pressed bubble. Background `#FFFFFF` with shadow `rgba(0,0,0,0.12) 0 6px 20px` in light, `#2C2C2E` with a 1pt separator border in dark. Six 28pt glyphs in a row: ❤️ 👍 👎 😂 ‼️ ❓. Present with scale 0.7 → 1.0 spring on ~500ms long-press; on tap, dock the chosen reaction as a 16pt chip overlapping the bubble's owner-near top corner at -16pt y, with a soft haptic."

- "Build the iMessage typing indicator: an incoming-shaped gray bubble (`#E9E9EB`/`#26262A`, left-aligned, 19pt radius with 6pt tail) containing three 7pt circles in `tertiaryLabel`. Animate each circle scale 0.6 → 1.0 → 0.6 with a 0.2s stagger on a ~1.4s ease-in-out loop. Appears pinned to the bottom of the thread while the other party composes."

- "Create the iMessage compose bar: blur-backed (`.ultraThinMaterial`), 36pt tall field. Leading: 30pt circular `+` button, background `#E9E9EB`/`#2C2C2E`, `plus` glyph `secondaryLabel`. Center: pill text field, 18pt corner radius, 1pt `#C6C6C8`/`#38383A` border, transparent fill, placeholder 'iMessage' in `tertiaryLabel`. Trailing: 30pt circular send button with `arrow.up`, background `#007AFF` (or `#34C759` for SMS), `#FFFFFF` glyph, disabled gray when the field is empty; scale 0.92 on press."

- "Render the iMessage chat nav bar: 44pt + safe area, hairline `separator` bottom. Leading: `chevron.left` 17pt `#007AFF` back button with unread badge. Center: 34pt circular avatar stacked above the contact name (SF Pro 13pt w600) with a 9pt `chevron.down` disclosure. Trailing: `video` 22pt `#007AFF` FaceTime button. Use `.ultraThinMaterial` that intensifies as the thread scrolls under it."

### Iteration Guide
1. Canvas is white `#FFFFFF` in light, true black `#000000` in dark (OLED) — never off-white in dark
2. Outgoing iMessage is `#007AFF`, SMS fallback is `#34C759` — the blue/green split is the brand and is culturally fixed
3. Incoming bubbles are neutral gray `#E9E9EB` / `#26262A` — never tinted; the contrast against blue is intentional
4. Bubbles are flat (no shadow), ~19pt radius, with one pinched ~6pt tail corner on the sender's edge
5. Group same-sender messages at 3pt, separate sender turns at 9pt; only the last bubble of a run shows the tail
6. Bubble max width is ~78% so the empty opposite margin is the directional signal — never full-width
7. Typography is pure San Francisco at Dynamic Type sizes — no custom brand font ever
8. The tapback strip (6 glyphs, capsule, long-press, docked chip) is the most-copied iMessage interaction — get it exact
9. Typing = three pulsing gray dots in an incoming-shaped bubble; receipts = tiny gray "Delivered"/"Read 9:41 AM" right-aligned
10. Depth is Apple blur material on nav/input bars, not drop shadows on content; only the tapback/context popover floats with a shadow
