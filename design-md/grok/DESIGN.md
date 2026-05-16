# Design System Inspiration of Grok (iOS)

## 1. Visual Theme & Atmosphere

Grok's iOS app is a true-black terminal for a conversation with an AI that lives inside X. The canvas is pure `#000000` — not the softened near-black of media apps, but the OLED-true black that X itself adopted. Against that void, the conversation reads like a transcript: there are no chat bubbles competing for attention, no gradient flourishes, no rounded cards stacked in a feed. The screen is mostly empty space and text, and that emptiness is the point — Grok wants the model's words and the X posts it cites to be the only things on screen. It is the design equivalent of a command line that happens to be having a conversation with you.

The accent system is deliberately anti-accent. There is no brand color doing decorative work. The interface is monochrome — white text (`#E7E9EA`) on black, with a single grey step (`#71767B`) for secondary metadata, and the only chromatic moment is X's link blue (`#1D9BF0`) on tappable post citations and inline links. White itself functions as the "accent": the send button, the active mode pill, the streaming cursor are all white-on-black or black-on-white. This restraint is inherited directly from X's redesigned identity, where the bird became an X and color was stripped out in favor of typographic confidence.

Typography is Inter — a grotesque sans chosen for its slashed-zero, tabular clarity and its faintly technical, machine-readable feel. Weights stay in a tight band of 400 / 600 / 700. There are no display headlines because Grok has no marketing surface inside the conversation; the largest type is a 28pt screen title, and the workhorses are 16pt body for the conversation and 13pt for citation metadata. The single most expressive typographic moment is the streaming response: tokens reveal left-to-right with a blinking block cursor (`▍`) trailing the text, so you watch the model think in real time. The type does not animate in size or weight — it appears, character by character, like a teletype.

**Key Characteristics:**
- True-black canvas (`#000000`) inherited from X — OLED black, not softened
- Monochrome by design — white text, one grey, no decorative accent color
- X link blue (`#1D9BF0`) is the only chroma — reserved for post citations and links
- Conversation as transcript — user messages right-aligned in a subtle bubble, assistant replies plain-text full-width
- Real-time X post citation cards — Grok's signature: it pulls live posts and embeds them inline
- Mode toggle (Regular / Fun) — a segmented pill at the top that changes Grok's personality
- Streaming token reveal with a blinking block cursor — you watch the answer being written
- Inter typeface with slashed zero — technical, machine-readable, weights 400/600/700
- Minimal chrome — a top bar and a prompt bar; no bottom tab navigation
- Dark-only — there is no light mode; the void is the brand

## 2. Color Palette & Roles

### Primary
- **Accent White** (`#FFFFFF`): The functional "accent" — active mode pill background, send-button fill (enabled), streaming cursor, focused-state outlines.
- **Pressed White** (`#D7DBDC`): Pressed/active state for white-filled controls (send button down).

### Canvas & Surfaces
- **Canvas Black** (`#000000`): The primary canvas — OLED-true black, the entire conversation backdrop.
- **Surface 1** (`#16181C`): User message bubble, prompt-bar field background, low-elevation containers.
- **Surface 2** (`#1E2126`): Citation card background, sheets, menus, elevated rows.
- **Surface 3** (`#272A2E`): Pressed/hover state on dark surfaces, suggestion chips when active.
- **Divider** (`#2F3336`): Hairline dividers, citation-card borders, prompt-bar top border.

### Text
- **Text Primary** (`#E7E9EA`): Assistant response body, user message text, screen titles — X's near-white, intentionally not pure `#FFFFFF` for reading comfort.
- **Text Secondary** (`#71767B`): Timestamps, citation handles, metadata, placeholder text, disabled labels.
- **Text Tertiary** (`#4D5156`): Very low-emphasis hints, character counters at rest.

### Semantic
- **Link Blue** (`#1D9BF0`): Inline links, the tappable region on X post citation cards, "View on X" affordances — the only chromatic color in the UI.
- **Link Blue Pressed** (`#1A8CD8`): Pressed state for links and citation taps.
- **Verified Blue** (`#1D9BF0`): Verified-account checkmark inside citation cards (same as link blue).
- **Success Green** (`#00BA7C`): Copy-confirmed toast, "Response copied".
- **Error Red** (`#F4212E`): Generation error, rate-limit, connection-lost banner.
- **Warning Amber** (`#FFD400`): "Grok can make mistakes" disclaimer emphasis (rare).

### Mode Identity
- **Regular Mode Indicator** (`#E7E9EA`): The default, factual personality — neutral white pill text.
- **Fun Mode Indicator** (`#1D9BF0`): The irreverent personality — the active "Fun" pill picks up link blue as a subtle signal.

### Light Mode
Grok's iOS app is dark-only. There is no light variant — the true-black void is intrinsic to the brand identity inherited from X. (For completeness, an inverted reference would be Canvas `#FFFFFF`, Text `#0F1419`, Surface `#F7F9F9`, Divider `#EFF3F4`, but it is not shipped.)

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (variable) — chosen for its slashed zero, tabular figures, and technical, machine-readable tone
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace (code blocks)**: `'SF Mono', ui-monospace, 'JetBrains Mono', Menlo, monospace`
- **Slashed zero enabled**: `font-feature-settings: 'zero' 1` — reinforces the technical identity in numerals and code

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 28pt | 700 | 1.2 | -0.4pt | "Grok" launch / history screen large title |
| Section Header | Inter | 20pt | 700 | 1.25 | -0.3pt | "Today", "Yesterday" in chat history |
| Conversation Title | Inter | 17pt | 600 | 1.3 | -0.2pt | Saved conversation row title |
| Assistant Body | Inter | 16pt | 400 | 1.55 | 0pt | The model's streamed response — the reading surface |
| User Message | Inter | 16pt | 400 | 1.45 | 0pt | Text inside the right-aligned user bubble |
| Prompt Input | Inter | 16pt | 400 | 1.4 | 0pt | Text typed into the prompt bar |
| Mode Pill Label | Inter | 14pt | 600 | 1.0 | 0pt | "Regular" / "Fun" segmented control text |
| Citation Author | Inter | 14pt | 700 | 1.3 | -0.1pt | Display name on an X post citation card |
| Citation Handle / Meta | Inter | 13pt | 400 | 1.3 | 0pt | "@handle · 2h", post timestamps, source count |
| Body Small | Inter | 14pt | 400 | 1.5 | 0pt | Suggestion chips, secondary explanatory text |
| Code | SF Mono | 13.5pt | 400 | 1.5 | 0pt | Inline + fenced code blocks (slashed zero on) |
| Button (Primary) | Inter | 15pt | 600 | 1.0 | 0pt | "New Chat", sheet confirm actions |
| Caption / Disclaimer | Inter | 12pt | 400 | 1.35 | 0pt | "Grok can make mistakes. Verify outputs." |
| Label (UPPER) | Inter | 11pt | 700 | 1.2 | 0.6pt | "SOURCES", "GROK 4" model badge |

### Principles
- **Weight band 400 / 600 / 700**: Regular for all body and conversation; semibold for controls and citation authors; bold for titles and labels. No light, no black.
- **Generous body leading**: Assistant responses run at 1.55 line-height — this is a reading surface, not a dense list. The whitespace is the design.
- **Slashed zero is intentional**: Numerals and code use `'zero' 1` to reinforce the technical, terminal feel — never disable it.
- **No tracking exaggeration**: Titles take at most -0.4pt; body and code sit at exactly 0pt for monospace alignment.
- **Color is structural, not typographic**: Link blue is the only non-monochrome text, and only on links/citations — body text is never colored for emphasis (use weight instead).

## 4. Component Stylings

### Buttons

**Send Button (Prompt Bar)**
- Shape: Circle, 32pt diameter, sits at the trailing edge of the prompt bar
- Disabled (empty input): background `#272A2E`, arrow glyph `#71767B`
- Enabled: background `#FFFFFF`, SF Symbol `arrow.up` 16pt in `#000000`
- Pressed: background `#D7DBDC`, scale 0.92, soft haptic
- During streaming: morphs to a `stop.fill` square in a `#272A2E` circle (tap to halt generation)

**New Chat Button**
- Shape: Pill, height 36pt
- Background: transparent with 1pt border `#2F3336`
- Text: `#E7E9EA`, Inter 15pt weight 600, leading SF Symbol `square.and.pencil` 16pt
- Padding: 8pt vertical, 16pt horizontal
- Pressed: background `#16181C`, border `#2F3336`

**Mode Toggle (Regular / Fun)**
- Container: pill, 36pt tall, background `#16181C`, 1pt border `#2F3336`, corner radius 18pt
- Two segments, each 14pt weight 600 label, 14pt horizontal padding
- Inactive segment: text `#71767B`, transparent background
- Active segment: background `#FFFFFF`, text `#000000` (Regular) — for "Fun", active text is `#1D9BF0`-tinted on a `#16181C` highlight to signal personality
- Selection animates with a 200ms spring sliding indicator, soft haptic on switch

**Icon Actions (Copy, Regenerate, Share, Thumbs)**
- Size: 18pt glyph, 44pt hit area
- Default: `#71767B`
- Pressed: `#E7E9EA`
- Confirmed (copy done): briefly `#00BA7C` with a checkmark swap for 1.2s
- Spacing: 20pt between icons in the response action row

**Suggestion Chip (Prompt Starters)**
- Background: `#16181C`, 1pt border `#2F3336`
- Text: `#E7E9EA`, Inter 14pt weight 400
- Padding: 10pt vertical, 14pt horizontal, corner radius 14pt
- Pressed: background `#272A2E`
- Used on the empty state: "Summarize this thread", "What's happening in tech?"

### Cards & Containers

**User Message Bubble**
- Alignment: right-aligned, max-width ~78% of viewport
- Background: `#16181C` (subtle — barely lifted from black)
- Text: `#E7E9EA`, Inter 16pt weight 400
- Padding: 12pt vertical, 16pt horizontal
- Corner radius: 20pt, with the bottom-right corner reduced to 6pt (tail toward the sender)
- No avatar, no name label — position alone disambiguates speaker

**Assistant Response**
- Alignment: full-width, left-aligned, **no bubble** — plain text directly on the `#000000` canvas
- Text: `#E7E9EA`, Inter 16pt weight 400, line-height 1.55
- Leading: a 24pt Grok glyph mark sits above the first line on initial response, not per-paragraph
- Markdown rendered: bold `#E7E9EA` weight 700, links `#1D9BF0`, code blocks in `#16181C` with `#2F3336` border
- Trailing: response action row (copy / regenerate / share / 👍 👎) appears after streaming completes

**X Post Citation Card** *(signature)*
- Background: `#1E2126`, 1pt border `#2F3336`, corner radius 16pt
- Padding: 14pt all sides, margin: 8pt vertical between stacked citations
- Header row: 28pt circular avatar → display name (14pt w700 `#E7E9EA`) + verified check (`#1D9BF0` 14pt) → "@handle · 2h" (13pt w400 `#71767B`) → trailing X logo glyph 16pt `#71767B`
- Body: post text, 14pt w400 `#E7E9EA`, 4-line clamp
- Footer: engagement metrics (reply / repost / like counts) in 12pt `#71767B` with 14pt glyphs
- Entire card tappable → opens the post on X; pressed state tints border `#1D9BF0` and background `#272A2E`

**Sources Strip**
- Appears above a response that used live X data
- Label "SOURCES" 11pt w700 `#71767B` letter-spacing 0.6pt
- Horizontal scroll of mini source pills: 28pt tall, `#1E2126` background, `#2F3336` border, avatar + handle in 12pt `#E7E9EA`

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Background: `#000000` (no blur — the canvas is already black; a blur would be invisible)
- Left: history/menu SF Symbol `line.3.horizontal` 20pt `#E7E9EA`
- Center: the Mode Toggle pill (Regular / Fun) — this is the primary nav affordance, not a title
- Right: `square.and.pencil` (new chat) 20pt `#E7E9EA`
- A 0.5pt `#2F3336` divider appears only when content scrolls beneath it

**No Bottom Tab Bar**
- Grok is single-surface — there is no tab navigation. History is a slide-over panel from the left (`#000000`, list of conversation rows with 1pt `#2F3336` dividers). Settings is a pushed screen. The conversation owns the entire viewport.

**History Slide-Over**
- Width: ~84% of viewport, background `#000000`
- Rows: 56pt tall, conversation title 16pt w400 `#E7E9EA`, timestamp 13pt `#71767B`
- Section headers: "Today" / "Previous 7 Days" — 13pt w700 `#71767B`
- Pressed row: background `#16181C`

### Input Fields

**Prompt Bar** *(the persistent composer)*
- Pinned to the bottom, above the safe area
- Container: background `#16181C`, corner radius 24pt, 1pt border `#2F3336`
- Min height: 48pt, grows to a 5-line max then scrolls internally
- Placeholder: "Ask Grok anything", 16pt w400 `#71767B`
- Text: `#E7E9EA` 16pt w400
- Leading: attachment `paperclip` SF Symbol 18pt `#71767B` (8pt inset)
- Trailing: the 32pt Send button (state per Buttons section)
- Focus: border brightens to `#3E4144`, no color shift — Grok stays monochrome even in focus
- A 0.5pt `#2F3336` divider separates the prompt bar zone from the conversation above it

**Inline Code & Code Block**
- Inline: `#16181C` background, `#2F3336` 0.5pt border, 4pt radius, SF Mono 13.5pt, `#E7E9EA`
- Block: `#16181C` background, 12pt radius, 14pt padding, language label 11pt w700 `#71767B` top-right, a `doc.on.doc` copy icon that confirms in `#00BA7C`

### Distinctive Components

**Streaming Token Reveal** *(signature motion)*
- The assistant response renders token-by-token left-to-right
- A block cursor `▍` (`#E7E9EA`) trails the last character, blinking at ~1.06s (530ms on / 530ms off)
- On completion the cursor fades out over 200ms and the action row fades in
- Tapping the morphed Stop button freezes the stream and the cursor disappears immediately

**Mode Personality Shift**
- Switching Regular ↔ Fun slides the pill indicator (200ms spring) and, on the next response, subtly changes tone
- "Fun" active state is the only place the UI lets blue (`#1D9BF0`) tint a control label — a quiet personality tell

**Real-Time X Integration**
- When a prompt needs current data, a "Searching X…" inline status appears with a small animated pulse on a `globe` glyph, then resolves into the Sources strip + citation cards
- This live-data loop is Grok's defining differentiator — the citation card is its signature surface

**Empty State**
- Centered Grok wordmark/glyph, the Mode Toggle, and a vertical stack of 3-4 suggestion chips
- No illustration, no gradient — pure black with type, consistent with the void identity

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard horizontal margin: 16pt
- Conversation turn spacing: 24pt between a user message and the assistant response, 32pt between full turns

### Grid & Container
- Single-column, full device width with 16pt horizontal margins
- Assistant response: full content width (no card inset) for maximum reading width
- User bubble: right-aligned, max 78% width
- Citation cards: full content width minus 16pt margins, stacked vertically with 8pt gaps

### Whitespace Philosophy
- **Emptiness is the design**: The conversation is mostly black space. Generous 1.55 leading and 24-32pt turn gaps make the transcript breathe — the opposite of a dense feed.
- **No decorative containers**: The assistant reply has no bubble precisely so nothing frames the words. Only the user message and citation cards get surfaces.
- **The prompt bar is sacred**: It is always present, always reachable by thumb, never scrolled away.

### Border Radius Scale
- Square (0pt): Code-block inner selection (rare)
- Soft (4pt): Inline code
- Standard (14-16pt): Suggestion chips, citation cards, code blocks
- Comfortable (20pt): User message bubble (with 6pt tail corner)
- Composer (24pt): The prompt bar container
- Pill (18pt / full): Mode toggle, New Chat button
- Circle (50%): Send button, avatars

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Assistant responses, canvas — everything reads on pure black |
| Surface (Level 1) | No shadow; `#16181C` fill + `#2F3336` 1pt border | User bubble, prompt bar — elevation via value, not shadow |
| Card (Level 2) | No shadow; `#1E2126` fill + `#2F3336` 1pt border | X post citation cards |
| Sheet (Level 3) | `rgba(0,0,0,0.7) 0 -12px 40px` + `#1E2126` fill | Bottom sheets, share, model picker |
| Slide-Over | `rgba(0,0,0,0.6) 0 0 40px` on the leading edge | History panel |
| Scrim | `rgba(0,0,0,0.6)` | Behind sheets and the history slide-over |

**Shadow Philosophy**: On a true-black canvas, drop shadows are nearly invisible — so Grok almost never uses them. Elevation is communicated by *surface value* (`#000000` → `#16181C` → `#1E2126`) and 1pt `#2F3336` borders, not by blur. Shadows appear only on overlays that float above the conversation (sheets, slide-over), where a deep 40pt black shadow separates the layer from the void beneath it. Everything in the conversation itself is resolutely flat.

### Motion
- **Streaming cursor**: block `▍` blink, ~530ms on / 530ms off, continuous while generating
- **Token reveal**: text appears left-to-right as tokens arrive (no per-character spring — it is teletype-literal)
- **Send tap**: button scale 0.92 → 1.0 spring (response 0.25, damping 0.7) + `.impactOccurred(.soft)`
- **Send → Stop morph**: 200ms crossfade between the arrow circle and the stop square
- **Mode toggle**: indicator slides 200ms spring (damping 0.8) + soft haptic
- **Citation card tap**: border/background tint 120ms ease, then push to X
- **Copy confirm**: glyph swaps to checkmark, color animates to `#00BA7C` over 150ms, reverts after 1.2s
- **Searching X pulse**: `globe` glyph opacity 0.4 ↔ 1.0, 900ms ease-in-out loop until results resolve

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — Grok inherits X's OLED-true black
- Keep the conversation monochrome — white text, one grey, no decorative accent
- Reserve link blue `#1D9BF0` exclusively for links and X post citation taps
- Render the assistant response as plain full-width text — no bubble
- Give the user message a subtle `#16181C` bubble, right-aligned, with a 6pt tail corner
- Use Inter with slashed zero (`'zero' 1`) — the technical feel is intentional
- Stream tokens left-to-right with a blinking block cursor `▍`
- Communicate elevation with surface value + 1pt borders, not shadows
- Keep the prompt bar pinned to the bottom and always reachable
- Let the X post citation card be the visual hero — it is Grok's signature

### Don't
- Don't soften the canvas to `#121212` or `#0B0B0F` — Grok is true black like X
- Don't add a brand accent color — the monochrome restraint is the identity
- Don't wrap the assistant response in a chat bubble — it must be plain text
- Don't color body text for emphasis — use weight 700, never a hue
- Don't drop-shadow in-conversation elements — they vanish on black anyway; use value
- Don't add a bottom tab bar — Grok is single-surface; history is a slide-over
- Don't disable the slashed zero — it is core to the technical identity
- Don't animate the token reveal with bouncy per-character springs — it is teletype, literal
- Don't use light weights — Inter starts at 400 here
- Don't put a light mode in front of the user — Grok is dark-only by brand

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | User bubble max-width 80%; prompt bar min-height stays 48pt |
| iPhone 13/14/15 | 390pt | Standard layout; citation cards full content width |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the 44pt top bar |
| iPhone 15/16 Pro Max | 430pt | Wider reading column; assistant body unchanged at 16pt |
| iPad | 768pt+ | Conversation centered, max content width ~720pt; history becomes a persistent left column |

### Dynamic Type
- Assistant body, user message, conversation titles: full scale
- Citation handle / meta and the disclaimer caption: fixed (layout-sensitive at 12-13pt)
- Mode pill label and Send glyph: fixed size (control geometry must not break)
- Code blocks: scale modestly, capped at 16pt to preserve monospace columns

### Orientation
- Conversation: **portrait-locked** on iPhone (the reading column is vertical by intent)
- iPad: **rotates** — landscape widens the centered reading column and keeps history pinned left
- Code blocks: horizontally scroll internally rather than reflow on rotation

### Touch Targets
- Send button: 32pt glyph in a 44pt effective hit area
- Mode toggle segments: full segment height (36pt) tappable
- Citation card: entire card is a single 44pt+ tap target
- Response action icons: 18pt glyph, 44pt hit area, 20pt spacing

### Safe Area Handling
- Top: 44pt bar respects the notch / Dynamic Island
- Bottom: the prompt bar floats above the home indicator with safe-area inset padding
- Sides: 16pt content insets; citation cards inset to match
- Keyboard: the prompt bar rises with the keyboard; the conversation scrolls to keep the latest turn visible

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Surface 1 (user bubble, prompt bar): `#16181C`
- Surface 2 (citation cards, sheets): `#1E2126`
- Surface 3 (pressed): `#272A2E`
- Divider: `#2F3336`
- Text primary: `#E7E9EA`
- Text secondary: `#71767B`
- Accent white (controls): `#FFFFFF`
- Link blue (links, citations): `#1D9BF0`
- Success green (copied): `#00BA7C`
- Error red: `#F4212E`

### Example Component Prompts
- "Create a SwiftUI Grok user message bubble: right-aligned, max-width 78%, background #16181C, text #E7E9EA Inter 16pt weight 400, padding 12pt vertical / 16pt horizontal, corner radius 20pt with the bottom-right corner at 6pt. No avatar, no name."
- "Build the Grok prompt bar: pinned to bottom above the safe area, container #16181C, 24pt corner radius, 1pt #2F3336 border, min-height 48pt growing to 5 lines. Placeholder 'Ask Grok anything' in #71767B 16pt. Leading paperclip icon #71767B 18pt. Trailing 32pt circular Send button — disabled state #272A2E with #71767B arrow, enabled state #FFFFFF with #000000 SF Symbol 'arrow.up', and during streaming morph to a stop.fill square."
- "Design the X post citation card: background #1E2126, 1pt #2F3336 border, 16pt radius, 14pt padding. Header: 28pt circular avatar, display name Inter 14pt weight 700 #E7E9EA, verified check #1D9BF0 14pt, '@handle · 2h' 13pt #71767B, trailing X glyph 16pt #71767B. Body: post text 14pt #E7E9EA 4-line clamp. Footer: reply/repost/like counts 12pt #71767B. Entire card tappable; pressed tints border #1D9BF0 and background #272A2E."
- "Build the Regular/Fun mode toggle: pill container 36pt tall, #16181C background, 1pt #2F3336 border, 18pt radius. Two segments with 14pt weight 600 labels and 14pt horizontal padding. Inactive text #71767B; active 'Regular' is #FFFFFF background with #000000 text; active 'Fun' uses a #16181C highlight with #1D9BF0-tinted text. Sliding indicator animates 200ms spring with a soft haptic."
- "Create the Grok streaming response: full-width plain text on #000000 (no bubble), #E7E9EA Inter 16pt weight 400 line-height 1.55. Render tokens left-to-right with a trailing block cursor '▍' in #E7E9EA blinking 530ms on / 530ms off. On completion fade the cursor out over 200ms and fade in a response action row (copy, regenerate, share, 👍 👎) with 18pt icons #71767B and 20pt spacing."

### Iteration Guide
1. Canvas is pure `#000000` — true black, inherited from X, never softened
2. The UI is monochrome — white, one grey, and link blue `#1D9BF0` only on links/citations
3. Assistant replies are plain full-width text with no bubble; only the user message and citation cards get surfaces
4. Elevation comes from surface value (`#000000` → `#16181C` → `#1E2126`) and 1pt `#2F3336` borders — almost never shadow
5. Typography is Inter with slashed zero on (`'zero' 1`); weights 400/600/700 only
6. Stream tokens with a literal blinking block cursor — teletype, not a bouncy spring
7. The X post citation card is the signature surface — make it the visual focal point
8. No bottom tab bar — Grok is single-surface; history slides over from the left
9. Grok is dark-only — do not surface a light theme
