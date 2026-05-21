# Design System Inspiration of Kik (iOS)

## 1. Visual Theme & Atmosphere

Kik is a username-based messenger whose entire product premise — and therefore its design — is **anonymity without a phone number**. You sign up with a username, not a digit; you find people by `@handle`, group code, or **Kik Code** (a circular dotted scannable glyph); and conversations never expose a phone number. This shapes every screen: the chat list leads with `@usernames` and group names, the search bar invites "people, groups, bots", and the contact surface is a username directory rather than a phone address book. The atmosphere is clean, light, and youthful — Kik's audience skews teen/young-adult, so the UI is friendly, rounded, and bot-and-discovery-forward.

Kik's brand color today is a bright sky **Kik Blue** `#00B0F0` (the modern rebrand). Its **heritage** color is the original **Kik Green** `#82BC23` — the lime-green that defined Kik for years and still appears as a legacy/heritage accent and in older brand assets. The current system leads with Blue: it's the logomark, the outgoing message bubble, primary buttons, the tab selection, and the Kik Code background; Green is a respectful nod, not the lead. The canvas is white (`#FFFFFF`) in light mode and a neutral near-black (`#121316`) in dark mode so the bright Blue stays vivid.

Conversations are a vertical stack of rounded bubbles: outgoing in Kik Blue `#00B0F0` with dark-cyan ink `#002A36` (for WCAG AA on the bright fill) and a small ~5pt tail on the bottom-right; incoming in light gray `#E9EAEC` (light) / `#25282D` (dark) with primary text and a tail on the bottom-left. Bubbles use a ~16pt radius. Kik's single most distinctive affordance is its **S / D / R receipt**: a tiny single-letter status — **S** (Sent, light gray), **D** (Delivered, gray), **R** (Read, Kik Blue) — shown beside the outgoing message and in the chat-list row. This terse one-letter system (rather than checkmarks or "Read 9:41") is uniquely Kik.

The other pillars: **bots** are first-class — the chat list flags bot accounts with a "BOT" tag and a rounded-square avatar, and a Bot Shop / Discover surface lets users add automated accounts (sketch bots, quiz bots, brand bots). **Kik Codes** are the QR-like circular scannable identity glyphs used to add friends, join groups, or follow bots without sharing a username verbally. Add the anonymous-username directory, group chats with `@mentions`, and a Discover/Bot tab, and you have Kik's complete identity. Color beyond Blue is minimal — Green as heritage, red `#F0473E` for destructive/errors, neutral grays for chrome. Typography is a clean, friendly humanist sans (Kik's brand face; the closest free substitute is **Inter** or **Open Sans**), with heavy weights for titles and the username headline.

**Key Characteristics:**
- Username-first identity — `@handles`, no phone number; find via username / group code / Kik Code
- Modern Kik Blue (`#00B0F0`) is the brand; heritage Kik Green (`#82BC23`) survives as a legacy accent
- White canvas (`#FFFFFF`) light / neutral near-black (`#121316`) dark
- Rounded ~16pt bubbles: outgoing Kik Blue with dark-cyan ink, incoming gray (`#E9EAEC` / `#25282D`), ~5pt tail
- S / D / R receipt — single-letter status: S (sent, gray), D (delivered, gray), R (read, Blue)
- Bots are first-class — "BOT" tag + rounded-square avatar; Bot Shop / Discover surface
- Kik Codes — circular dotted scannable identity glyphs for adding friends / groups / bots
- Anonymous-username directory instead of a phone contact list
- Group chats with `@mentions` and member usernames
- Discover / Bot tab — automated-account marketplace
- Clean friendly humanist sans (Kik brand face → Inter / Open Sans), heavy titles
- Bottom tab bar: Chats / People / Discover / Kik Code with bright-Blue active tint

## 2. Color Palette & Roles

### Primary (Interactive)
- **Kik Blue** (`#00B0F0`): The modern brand. Outgoing bubble, primary buttons, tab selection, Kik Code bg, links, send.
- **Kik Blue Deep** (`#0093C8`): Filled surfaces needing more contrast, gradients, hover.
- **Kik Blue Pressed** (`#0086B8`): Pressed state of Blue controls.
- **Kik Cyan** (`#4FCBF7`): Lighter accent, gradient top stop, links/outline text on dark.
- **Kik Green (Heritage)** (`#82BC23`): Legacy brand accent — heritage badges, "classic" theming, older assets only.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Thread + list background, primary light canvas.
- **Incoming Bubble** (`#E9EAEC`): Light gray incoming bubble.
- **Surface Gray** (`#F3F4F6`): Grouped list background, search field.
- **Row Pressed** (`#E7E8EB`): Pressed conversation/list rows.
- **Divider** (`#E1E2E5`): Hairline separators between conversation rows.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121316`): Neutral near-black thread + list background.
- **Dark Surface 1** (`#1B1D21`): Nav/input/tab bar, elevated surfaces.
- **Dark Surface 2** (`#25282D`): Incoming bubble, search field, bot tags, sheets.
- **Dark Divider** (`#31343A`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#16181B`): Incoming bubble text, usernames, primary labels.
- **Text on Blue** (`#002A36`): Outgoing bubble text — dark-cyan ink for WCAG AA on bright `#00B0F0`.
- **Text Secondary** (`#6C6F77` light / `#9CA0A8` dark): Previews, timestamps, "D" receipt.
- **Text Tertiary** (`#9A9DA5` light / `#686C74` dark): Placeholder, disabled, "S" receipt.
- **Text Primary Dark** (`#F1F2F4`): Incoming bubble text and labels on dark.

### Semantic
- **Error / Destructive** (`#F0473E`): Delete, block, report, failed-message.
- **Success** (`#82BC23`): Success toast (reuses heritage green), "added" confirmation.
- **Link** (`#0093C8` light / `#4FCBF7` dark): Inline links inside bubbles.
- **Selection Highlight** (`#00B0F0` @ 18%): Text selection within a bubble.

### S / D / R Receipt Color Matrix

| State | Letter | Color (Light) | Color (Dark) | Meaning |
|-------|--------|---------------|--------------|---------|
| Sent | `S` | `#9A9DA5` | `#686C74` | Left device, reached Kik servers |
| Delivered | `D` | `#6C6F77` | `#9CA0A8` | Arrived on recipient's device |
| Read | `R` | `#00B0F0` | `#00B0F0` | Recipient opened the chat |
| Sending | `…` | `#9A9DA5` | `#686C74` | In flight (pre-S) |
| Failed | `!` | `#F0473E` | `#F0473E` | Not delivered — tap to retry |

## 3. Typography Rules

### Font Family
- **Brand**: Kik's house humanist sans (proprietary; not redistributable).
- **Recommended Free Substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — closest clean humanist; or `Open Sans` (SIL OFL) for a slightly friendlier match.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: proportional by default; tabular in timestamps.
- **Usernames** render in the brand face (not monospace), weight 700, with the leading `@` kept same-weight.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Inter | 32pt | 800 | 1.2 | -0.4pt | "Chats" list title |
| Screen Title | Inter | 26pt | 800 | 1.2 | -0.3pt | "New Chat" / sheet headers |
| Title 3 | Inter | 22pt | 700 | 1.25 | -0.2pt | Username on profile / large header |
| Headline | Inter | 17pt | 700 | 1.3 | -0.2pt | `@username` in chat header |
| Row Title | Inter | 15pt | 700 | 1.3 | -0.1pt | Conversation name / group name in list |
| Body / Bubble | Inter | 14pt | 400 | 1.32 | 0pt | Message text (outgoing 500 for ink legibility) |
| Body Emphasis | Inter | 14pt | 600 | 1.32 | 0pt | `@mentions` in group bubbles |
| Preview | Inter | 13pt | 500 | 1.35 | 0pt | Last-message preview in chat list |
| Footnote | Inter | 13pt | 600 | 1.35 | 0pt | Section labels, profile meta |
| Caption | Inter | 11pt | 600 | 1.35 | 0.1pt | Timestamps, "BOT" tag, S/D/R label |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary buttons |
| Receipt Letter | Inter | 11pt | 700 | 1.0 | 0.2pt | The S / D / R single glyph |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Clean and youthful**: heavy 700/800 titles and username headlines feel confident and approachable.
- **Body stays light**: 14pt regular incoming; outgoing bumps to 500 so the dark-cyan ink stays crisp on bright Blue.
- **Dynamic Type supported**: titles, headline, row title, body, previews scale; timestamps, tab labels, the S/D/R glyph, "BOT" tag stay fixed.
- **Username is the identity**: `@handle` always weight 700, the `@` never dimmed or detached.
- **Dark-cyan ink on Blue bubble is non-negotiable** for WCAG AA — never white text on `#00B0F0`.

## 4. Component Stylings

### Buttons

**Primary Button**
- Shape: Pill, 22pt corner radius
- Background: `#00B0F0`
- Text: `#00323F` (dark-cyan ink for AA), Inter 16pt weight 700
- Padding: 13pt vertical, 28pt horizontal
- Pressed: `#0086B8` + scale 0.98
- Disabled: `#00B0F0` @ 35% opacity

**Outline Button (secondary)**
- Background: transparent
- Border: 1.5pt `#00B0F0`
- Text: `#0093C8` (light) / `#4FCBF7` (dark), Inter 15pt weight 700
- Corner radius: 22pt
- Pressed: `#00B0F0` @ 10% fill

**Heritage Button (legacy/optional)**
- Background: `#82BC23` (the original Kik Green), text `#1B2A06`, Inter 15pt w700, 22pt radius
- Used only for explicitly "classic"/heritage theming — not the default CTA

**Text Button**
- Background: transparent
- Text: `#0093C8` / `#4FCBF7`, Inter 15pt weight 600 ("Scan Kik Code", "Add Username")
- Pressed: opacity 0.5

**Send Affordance**
- Kik uses a text "Send" label (Inter 14pt w700 `#00B0F0`) at the trailing edge of the input bar rather than a circular icon button (heritage Kik pattern); a `+` attachment glyph leads the bar

### Message Bubble (the core atom)

**Outgoing Bubble**
- Background: `#00B0F0`
- Text: `#002A36` (dark-cyan ink), Inter 14pt w500
- Padding: 8pt vertical, 13pt horizontal
- Corner radius: 16pt, bottom-right corner at 5pt (tail) when last in a same-sender run
- Alignment: trailing, max width ~80% of thread
- Footer: timestamp 10pt + S/D/R letter, right-aligned

**Incoming Bubble**
- Background: `#E9EAEC` light / `#25282D` dark
- Text: `#16181B` / `#F1F2F4`, Inter 14pt w400
- Corner radius: 16pt, bottom-left corner at 5pt (tail)
- Alignment: leading; in group chats, sender `@username` in 14pt w600 `#0093C8` above the first bubble

**Grouping**
- Same sender consecutive: 4pt gap, only last bubble shows the tail
- Sender change: 8pt gap
- Day separator: centered 11pt caption, `secondaryLabel`, 8pt vertical margin

**States**
- Sending: `…` glyph in place of the receipt letter, bubble ~70% opacity
- Failed: red `!`, tap to resend
- Bot reply: may include inline buttons / suggested-reply chips (rounded, `#00B0F0` outline)
- Reply / forward: a thin Blue left-bar + quoted snippet inside the bubble

### S / D / R Receipt

- A single uppercase letter beside the timestamp (in the bubble footer) AND in the chat-list row's meta column
- **S** — Sent: `#9A9DA5` / `#686C74`
- **D** — Delivered: `#6C6F77` / `#9CA0A8`
- **R** — Read: `#00B0F0` (Kik Blue) in both modes — the one moment color enters the receipt
- Inter 11pt w700, +0.2pt tracking; transitions S → D → R by swapping the glyph (no animation, instant)

### Conversation List Row

- Height: 72pt, hairline `divider` bottom
- Leading: 48pt avatar — circular for people, **rounded-square (14pt)** for bots
- Mid: name/`@username` (15pt w700) + a "BOT" tag (11pt w700, `surface` pill) for bot accounts; preview (13pt `secondaryLabel`, 1-line, "@mentioner: …" in groups)
- Trailing meta column: timestamp (11pt `tertiaryLabel`) above either the unread count (Blue pill, `#00323F` ink) or the outgoing S/D/R letter

### Kik Code

- A circular scannable identity glyph: a solid `#00B0F0` rounded-square plate containing a dotted ring and a central glyph/photo
- Used to add a friend, join a group, or follow a bot by scanning (no username typed)
- Appears on the profile screen, the dedicated Kik Code tab, and in share sheets
- Rounded-square plate radius ~24pt; the dotted ring is white on the Blue plate

### Navigation

**Chat List Header**
- Large title "Chats" (26–32pt w800), trailing actions: new-chat (`plus`) + camera/scan, tinted `#00B0F0`
- Search field below: 36pt, `#F3F4F6` / `#25282D`, 10pt radius, placeholder "Search for people, groups, bots"

**Chat Header**
- Height: 56pt + safe area, hairline `divider` bottom
- Leading: back chevron (`chevron.left` 17pt `#00B0F0`)
- Center: `@username` (Inter 17pt w700) or group name; small member-count subtitle for groups
- Trailing: profile/avatar tap → user/group settings (no call icons — Kik is text/bot-first, not call-first)

**Bottom Tab Bar**
- Height: 50pt + safe area, background `#FFFFFF` / `#1B1D21`, hairline top divider
- Tabs (4): Chats, People, Discover (bots), Kik Code
- Icon 22pt; active = `#00B0F0` filled glyph + label; inactive = `#9A9DA5` / `#686C74`
- Labels: Inter 11pt w600 always shown; unread badge on Chats (Blue pill)

### Input Fields

**Compose Bar**
- Background: `#FFFFFF` / `#1B1D21`, hairline top divider, 8/16pt padding
- Leading: `+` 22pt attachment (camera, gallery, GIF, web, Kik content)
- Center: pill field — 18pt radius, `#F3F4F6` / `#25282D` fill, placeholder "Type a message" `tertiaryLabel`
- Trailing: text "Send" label, Inter 14pt w700 `#00B0F0` (dimmed `tertiaryLabel` when empty)

**Search Bar**
- Height: 36pt, `#F3F4F6` / `#25282D`, 10pt radius
- Leading `magnifyingglass` 15pt `tertiaryLabel`, placeholder "Search for people, groups, bots"

### Distinctive Components

**S / D / R Receipt** — Kik's terse single-letter status (Sent / Delivered / Read), Blue at "R"; the most-recognized Kik affordance.

**Anonymous Username Identity** — `@handles` everywhere, no phone number; the product's whole premise.

**Kik Code** — circular dotted scannable identity glyph on a Blue rounded-square plate; add-by-scan.

**Bot Accounts** — "BOT" tag + rounded-square avatar in lists; inline bot reply buttons / suggested-reply chips.

**Discover / Bot Shop** — a marketplace tab for adding automated accounts.

**Group Chat with @mentions** — username-based group rooms; preview shows "@mentioner: …".

**Username Directory (People)** — the contact surface is a searchable username list, not a phone book.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 44
- Same-sender bubble gap: 4pt
- Sender-change gap: 8pt
- Thread horizontal inset: 14pt, bubble max width ~80%
- Bubble internal padding: 8pt vertical / 13pt horizontal
- List row height: 72pt; avatar 48pt

### Grid & Container
- iPhone: full-width thread + list, 14–16pt gutters; bubble max ~80%
- Kik Code: a fixed ~120pt rounded-square plate, centered on profile/scan screens
- iPad: split — conversation list (~340pt) + thread; content max ~680pt centered
- Headers, search, tab bar: full-width, safe-area aware

### Whitespace Philosophy
- **Friendly density**: 4pt within a run, 8pt on sender change — conversational and youthful
- **Blue is the only saturated surface**: chrome is neutral so the bright Blue bubble and CTAs pop
- **Username-forward whitespace**: the list leads with `@handles`; previews are single-line so identity reads fast
- **Bots are visually distinct**: rounded-square avatars + "BOT" tag break the circular-avatar rhythm so automation is obvious at a glance

### Border Radius Scale
- Square (0pt): full-bleed media (rare)
- Tail (5pt): the pinched bubble corner on the sender's edge
- Subtle (10pt): search bar, list thumbnails
- Standard (14pt): bot avatars, reply-quote container, suggested-reply chips
- Bubble (16pt): the message bubble body radius
- Pill (22pt / 500pt): primary buttons, unread badges
- Plate (24pt): the Kik Code rounded-square plate
- Circle (50%): person avatars, presence-free identity circles

## 6. Depth & Elevation

Kik is mostly flat with soft neutral shadows on floating surfaces; the Blue brand and gradient identity circles carry visual interest instead of heavy elevation.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Bubbles, list rows, thread content |
| Raised (Level 1) | `rgba(0,0,0,0.10) 0 2px 8px` | Floating new-chat FAB, raised cards |
| Floating (Level 2) | `rgba(0,0,0,0.16) 0 6px 20px` (light) / 1pt border on dark | Context menu, attachment popover |
| Sheet (Level 3) | `rgba(0,0,0,0.20) 0 -4px 24px` | Attachment sheet, share, Kik Code share |
| Modal Overlay | `rgba(0,0,0,0.5)` dim | Behind modals and sheets |

**Shadow Philosophy**: Kik uses soft neutral shadows only on truly floating surfaces (menus, FAB, sheets); content (bubbles, list rows, the Kik Code plate) stays flat. Identity avatars use Blue/cyan gradients as the visual-interest layer instead of shadows. On dark mode shadows fade, so floating surfaces gain a faint 1pt `divider` border. The Kik Code plate is intentionally flat and high-contrast so it scans reliably.

### Motion
- **Bubble send**: outgoing bubble slides up + scales 0.88 → 1.0 from the input field, `spring(response: 0.32, damping: 0.8)` + light haptic
- **Receipt change**: S → D → R swaps the letter instantly (no animation); the R recolor to Blue is the only color event
- **List row update**: new message bumps the row to the top with a 0.25s ease-out reorder; unread badge pops in
- **Bot reply buttons**: suggested-reply chips fade + 4pt slide-up in 0.2s
- **Kik Code scan**: scanner overlay; on match, the Kik Code plate pulses once and the chat opens with a push transition
- **Tab switch**: instant; active-icon fill + color crossfade 0.15s
- **Typing**: "typing…" text in the chat-header subtitle
- **Attachment sheet**: slides up from bottom, 0.3s ease-out
- **Haptics**: light impact on send, soft on bot-button tap, success on Kik Code match / friend added

## 7. Do's and Don'ts

### Do
- Lead with the modern Kik Blue `#00B0F0` — outgoing bubble, buttons, tabs, Kik Code plate
- Keep Kik Green `#82BC23` strictly as a heritage/legacy accent — never the default CTA
- Use white (`#FFFFFF`) light canvas / neutral near-black (`#121316`) dark — neutral keeps Blue vivid
- Render outgoing bubbles Kik Blue with dark-cyan ink `#002A36` (AA), incoming gray (`#E9EAEC` / `#25282D`), ~16pt radius with a ~5pt tail
- Use the S / D / R single-letter receipt; turn it Blue only at "R" (Read)
- Make identity username-first — `@handles` everywhere, never surface a phone number
- Flag bots distinctly — rounded-square avatar + "BOT" tag in the list
- Provide Kik Codes — a Blue rounded-square plate with a dotted ring, flat and high-contrast for scanning
- Make the People tab a searchable username directory, not a phone contact list
- Use a clean friendly humanist sans (Kik brand face → Inter/Open Sans), heavy 700/800 titles
- Use a text "Send" label at the input trailing edge (heritage Kik pattern)

### Don't
- Don't lead with Kik Green — the modern brand is Blue; Green is heritage-only
- Don't put white text on the bright `#00B0F0` bubble/button — use dark-cyan ink `#002A36`/`#00323F` for AA
- Don't tint incoming bubbles Blue — they're neutral gray; only outgoing is brand Blue
- Don't tint the dark canvas — Kik's dark mode is a neutral near-black `#121316`
- Don't expose phone numbers anywhere — Kik's premise is username-only anonymity
- Don't use circular avatars for bots — rounded-square + "BOT" tag is the bot signifier
- Don't replace S/D/R with checkmarks or "Read 9:41" — the single-letter system is uniquely Kik
- Don't add a second brand accent — Blue is it; Green is heritage, red is destructive only
- Don't put call/video icons in the header — Kik is text/bot-first, not call-first
- Don't shadow the Kik Code plate or bubbles — they're flat; the plate must stay high-contrast to scan
- Don't animate the receipt change — S/D/R swaps instantly; only the R-to-Blue recolor reads as an event

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max ~300pt; compact list rows |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above header |
| iPhone 15/16 Pro Max | 430pt | Wider bubbles (~344pt), larger Kik Code plate |
| iPad (portrait) | 768pt | Split: conversation list (~340pt) + thread; content max ~680pt centered |
| iPad (landscape) | 1024pt+ | Persistent list + thread; Kik Code shown larger on profile pane |

### Dynamic Type
- Titles, headline, row title, body, previews: full scale
- Timestamps, tab labels, the S/D/R glyph, "BOT" tag: FIXED
- Kik Code plate: fixed ~120pt (must keep scan geometry)

### Orientation
- Conversation supports rotation; bubbles reflow keeping the ~80% cap
- iPad keeps split view in both orientations; Kik Code plate stays fixed-size and centered

### Touch Targets
- `+` attachment / "Send" label: 44pt hit
- Bubble: full bubble is the long-press (copy/forward/reply) target
- List row / tab / bot suggested-reply chip: full-row / full-cell / ≥44pt
- Kik Code plate (tappable to enlarge/share): full plate

### Safe Area Handling
- Top: header respects safe area + Dynamic Island
- Bottom: input bar + tab bar respect home indicator; thread scrolls above keyboard
- Keyboard: compose bar + attachment sheet ride above the keyboard
- Sides: 14–16pt gutters; full-bleed only for media

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121316` (neutral near-black)
- Kik Blue (brand): `#00B0F0`
- Kik Blue deep: `#0093C8`
- Kik Green (heritage only): `#82BC23`
- Outgoing bubble: `#00B0F0`, text `#002A36`
- Incoming bubble: `#E9EAEC` light / `#25282D` dark
- Text primary: `#16181B` light / `#F1F2F4` dark
- Text secondary: `#6C6F77` light / `#9CA0A8` dark
- Receipt S: `#9A9DA5` / `#686C74`
- Receipt D: `#6C6F77` / `#9CA0A8`
- Receipt R: `#00B0F0` (both modes)
- Error: `#F0473E`
- Link: `#0093C8` / `#4FCBF7`

### Example Component Prompts
- "Build a Kik outgoing bubble in SwiftUI: background `#00B0F0`, text `#002A36` (dark-cyan ink for WCAG AA on bright blue) Inter 14pt weight 500, padding 8pt vertical / 13pt horizontal, corner radius 16pt with `.bottomTrailing` at 5pt (tail) when last in a same-sender run. Right-aligned, max width 80%. Footer: timestamp 10pt + the single S/D/R receipt letter. Incoming variant: background `#E9EAEC` (light) / `#25282D` (dark), text `label` 14pt w400, tail on `.bottomLeading`."

- "Create the Kik S/D/R receipt: a single uppercase letter beside the timestamp. S = Sent `#9A9DA5`/`#686C74`; D = Delivered `#6C6F77`/`#9CA0A8`; R = Read `#00B0F0` (Kik Blue, both modes). Inter 11pt w700, +0.2pt tracking. Transition S → D → R by swapping the glyph instantly (no animation); only the R recolor to Blue reads as a moment. Show the same letter in the chat-list row's meta column."

- "Render a Kik conversation row: 72pt tall, hairline divider. Leading 48pt avatar — a circle for people, a 14pt rounded-square for bots. Mid: name/`@username` Inter 15pt w700 (with a 'BOT' tag — 11pt w700 surface pill — for bots) + 1-line preview 13pt `secondaryLabel` ('@mentioner: …' in groups). Trailing meta column: timestamp 11pt `tertiaryLabel` above either an unread count (Kik Blue pill, `#00323F` ink) or the outgoing S/D/R letter."

- "Build a Kik Code: a flat `#00B0F0` rounded-square plate (~120pt, 24pt corner radius) containing a white dotted ring and a central glyph/photo. Keep it flat and high-contrast (no shadow) so it scans reliably. Used to add a friend / join a group / follow a bot by scanning — appears on the profile, the Kik Code tab, and share sheets."

- "Create the Kik compose bar: background `#FFFFFF`/`#1B1D21`, hairline top divider. Leading `+` 22pt attachment. Center: pill field 18pt radius, `#F3F4F6`/`#25282D` fill, placeholder 'Type a message' `tertiaryLabel`. Trailing: a text 'Send' label (Inter 14pt w700 `#00B0F0`, dimmed `tertiaryLabel` when empty) — Kik uses a text label, not a circular icon button."

- "Render the Kik bottom tab bar: 50pt + safe area, background `#FFFFFF`/`#1B1D21`, hairline top divider. Tabs: Chats, People, Discover, Kik Code. Icon 22pt; active = bright `#00B0F0` filled glyph + label; inactive = `#9A9DA5`/`#686C74`. Labels Inter 11pt w600 always shown; Kik Blue unread badge on Chats. No tint pill — icon fill + color change only. No call/video icons anywhere (Kik is text/bot-first)."

### Iteration Guide
1. The modern brand is Kik Blue `#00B0F0`; Kik Green `#82BC23` is heritage-only — never the default CTA
2. Put dark-cyan ink `#002A36`/`#00323F` on the bright Blue bubble/button — never white text (WCAG AA)
3. Dark canvas is a neutral near-black `#121316` — NOT color-cast; neutral keeps Blue vivid
4. Incoming bubbles are neutral gray `#E9EAEC` / `#25282D`; only the outgoing bubble is brand Blue
5. The S/D/R single-letter receipt is uniquely Kik — Sent/Delivered gray, Read turns Blue; swap instantly, never animate
6. Identity is username-first: `@handles` everywhere, no phone number ever surfaced — it's the product's premise
7. Bots are first-class and visually distinct — rounded-square avatar + "BOT" tag break the circular rhythm
8. Kik Codes are flat high-contrast Blue rounded-square plates with a dotted ring — add-by-scan, must scan reliably
9. No call/video chrome — Kik is text + bot + Discover-first, not call-first; the People tab is a username directory
10. Typography is a clean friendly humanist sans (Inter/Open Sans substitute) with heavy 700/800 titles; bubbles flat ~16pt with a ~5pt tail; "Send" is a text label
