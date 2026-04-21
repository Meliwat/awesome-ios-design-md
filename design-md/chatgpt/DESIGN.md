# Design System Inspiration of ChatGPT (iOS)

## 1. Visual Theme & Atmosphere

ChatGPT's iOS app is a master class in deliberate minimalism. The canvas is pure white (`#FFFFFF`) in light mode or near-black (`#212121`) in dark — there is almost no chrome, no color, no decoration. The design choice maps directly onto the product: ChatGPT is a conversation with a model, and the UI recedes so the *text itself* is the interface. Open a new chat and the screen is almost empty — a centered compose field at the bottom, a subtle "ChatGPT" model-selector chip at the top-left, and a "New chat" icon top-right. Type a message and everything scrolls upward as the conversation builds, with your messages on the right in soft-gray bubbles and the model's responses inline without a bubble — just plain text flowing down the page like a document.

The 2024 redesign (led in part by the Sam Altman-era direction) pushed even further toward true-black/true-white extremes, stripping out the legacy ChatGPT Green (`#10A37F`) that was once the accent. Today's palette is almost monochromatic: a deep black `#0D0D0D` for primary text in light mode, `#ECECEC` in dark, with a soft gray `#F7F7F8` for the user's message bubble background (light) / `#2F2F2F` (dark). The only color accent is a subtle blue (`#2A7FFF`) reserved for inline links and the occasional interactive chip. The assistant's response is just flowing prose — markdown-rendered with proper heading sizes, code blocks in a slightly tinted surface, and tables rendered cleanly, but no bubble framing around the text.

The identity-defining moment is the **send button** — a black circle with a white up-arrow (or white circle with black arrow in dark mode), 32pt diameter, sitting at the right end of the compose field. When the compose field is empty, it's replaced by a row of icons (paperclip for attachments, globe for web search, microphone for voice). When you type, those icons collapse and the send button appears. Press-and-hold the microphone to record voice; release to send as audio or transcription. There's also the unmistakable **voice mode** — tap the voice icon and the whole screen fades to a full-screen pulsing blue gradient sphere that listens and responds in natural voice.

Typography is **Söhne** — a proprietary, commercially-licensed face by Klim Type Foundry (the one used on openai.com). On iOS, ChatGPT falls back to Inter for some surfaces and to SF Pro (system) when neither is bundled. Söhne is a neo-grotesque that reads like Helvetica's more sophisticated cousin — crisper, more contemporary, with a touch of humanism. Hierarchy is minimal: chat messages at 16pt weight 400, inline code at 14pt Menlo, and markdown headings rendered at progressively larger sizes (H1 24pt, H2 20pt, H3 17pt). There are no UI headlines because the whole app is a document view.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / near-black (`#212121`) dark — minimum UI, maximum content
- No color accent — the post-2024 design is monochromatic, with blue (`#2A7FFF`) only on inline links
- User messages: soft-gray bubble (`#F7F7F8` light / `#2F2F2F` dark), right-aligned, rounded corners
- Assistant messages: NO bubble — plain text flowing inline with markdown rendering (code, tables, LaTeX, lists)
- Circular send button — black-on-white light / white-on-black dark — with up-arrow icon, 32pt
- Voice mode: full-screen pulsing blue gradient sphere — a meditative voice UI moment
- Model selector chip (`GPT-4o`, `GPT-4`, `GPT-5`) top-left — pill-shaped with leading model icon
- Sidebar: conversation history auto-titled, grouped by "Today / Yesterday / Previous 7 Days / Previous 30 Days"
- Compose field icon row: paperclip (attach), globe (web search), microphone (voice), send
- Thumbs up/down feedback icons below each assistant response
- Typing indicator: 3-dot pulse animation
- Söhne (or Inter / SF Pro fallback) — neo-grotesque, 400 / 500 / 600 weights, compact
- Code blocks with syntax highlighting + copy button, language-labeled

## 2. Color Palette & Roles

### Primary (Monochromatic)
- **Black** (`#0D0D0D`): Primary text on light mode, pure black feeling but softer than `#000`.
- **White** (`#FFFFFF`): Canvas light / pressed inverse.
- **Dark Canvas** (`#212121`): Primary canvas in dark mode — charcoal-black, NOT true black.
- **Dark Text Primary** (`#ECECEC`): Body text on dark.

### Send Button
- **Send Light** (`#0D0D0D`): Black circle in light mode.
- **Send Dark** (`#FFFFFF`): White circle in dark mode.
- **Send Icon Contrast**: Always the inverse of the circle color.
- **Send Disabled** (`#CCCCCC` light / `#4D4D4D` dark): When compose is empty.

### User Message Bubble
- **User Bubble Light** (`#F7F7F8`): Soft gray, barely distinguishable from canvas.
- **User Bubble Dark** (`#2F2F2F`): Mid-dark gray on dark mode.
- **User Bubble Text** (`#0D0D0D` / `#ECECEC`): Same as body text.

### Assistant
- **Assistant Text** (`#0D0D0D` / `#ECECEC`): Same as body text — no bubble, no box.
- **Assistant Inline Code Bg** (`#F0F0F0` / `#424242`): Inline `code` chip background.
- **Code Block Bg** (`#F7F7F8` / `#1E1E1E`): Full code block background.
- **Code Block Border** (`#E5E5E5` / `#363636`): 1pt border.

### Canvas & Surfaces (Light)
- **Canvas** (`#FFFFFF`)
- **Sidebar Bg** (`#F9F9F9`): Sidebar background — slightly darker than canvas.
- **Sidebar Active** (`#ECECEC`): Selected conversation row.
- **Sidebar Hover** (`#F0F0F0`)
- **Divider** (`#E5E5E5`)

### Canvas & Surfaces (Dark)
- **Canvas** (`#212121`)
- **Sidebar Bg** (`#181818`): Darker than canvas — signature treatment.
- **Sidebar Active** (`#2F2F2F`)
- **Sidebar Hover** (`#252525`)
- **Divider** (`#424242`)

### Text Hierarchy
- **Text Primary Light** (`#0D0D0D`)
- **Text Secondary Light** (`#676767`): Placeholder, timestamps, meta
- **Text Tertiary Light** (`#8E8E8E`): Disabled, very low-emphasis
- **Text Primary Dark** (`#ECECEC`)
- **Text Secondary Dark** (`#B4B4B4`)
- **Text Tertiary Dark** (`#707070`)

### Semantic
- **Link Blue** (`#2A7FFF`): Inline hyperlinks — subtle blue, very minimal usage
- **Error Red** (`#E53E3E`): Error toast, "message failed" state
- **Warning Orange** (`#D97706`): Content flag warnings, usage limit warnings
- **Success Green** (`#10A37F`): Historically ChatGPT's accent — now mostly retired, appears in older UI and the "ChatGPT" wordmark logotype
- **Voice Mode Sphere** (`#3B82F6` → `#60A5FA` → `#93C5FD`): The voice-mode full-screen pulsing sphere uses a blue gradient that animates organically

### Feedback & Interactions
- **Regenerate Bg** (`#F0F0F0` / `#363636`): Button background behind regenerate / thumbs-up / thumbs-down icons
- **Copy Button Bg** (`#F0F0F0`): "Copy" button on code blocks
- **Stop Button** (`#676767` → `#0D0D0D` on press): The "stop generating" button during streaming

## 3. Typography Rules

### Font Family
- **Primary (openai.com + app)**: `Söhne` (proprietary by Klim Type Foundry, licensed) — neo-grotesque, crisp, contemporary. Used on openai.com and in-app where licensed.
- **Fallback (web / some surfaces)**: `Inter` (SIL OFL, by Rasmus Andersson) — close visual substitute
- **System Fallback (iOS)**: `-apple-system, SF Pro Text, SF Pro Display` — iOS system default
- **Monospace (code)**: `Menlo` / `SF Mono` / `IBM Plex Mono` — all acceptable
- **Fallback Stack**: `"Söhne", -apple-system, BlinkMacSystemFont, "Inter", "Segoe UI", Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Chat Body (User/Assistant) | Söhne | 16pt | 400 | 1.5 | 0pt | Main message text |
| Chat Body (Compact) | Söhne | 15pt | 400 | 1.45 | 0pt | User setting: "Compact" density |
| Markdown H1 | Söhne | 24pt | 600 | 1.25 | -0.2pt | `# heading` rendered in assistant message |
| Markdown H2 | Söhne | 20pt | 600 | 1.3 | -0.15pt | `## heading` |
| Markdown H3 | Söhne | 17pt | 600 | 1.35 | -0.1pt | `### heading` |
| Markdown H4+ | Söhne | 16pt | 600 | 1.4 | 0pt | `#### heading` |
| Inline Code | Menlo | 14pt | 400 | 1.3 | 0pt | `\`code\`` — in colored chip |
| Code Block | Menlo | 13pt | 400 | 1.5 | 0pt | Fenced ```` code block |
| Model Selector Chip | Söhne | 14pt | 500 | 1.0 | 0pt | "GPT-4o" at top of chat |
| New Chat Title (auto-generated) | Söhne | 15pt | 500 | 1.3 | 0pt | Sidebar conversation titles |
| Sidebar Section | Söhne | 12pt | 500 | 1.0 | 0.2pt | "Today", "Yesterday", "Previous 7 Days" |
| Button | Söhne | 14pt | 500 | 1.0 | 0pt | Regenerate / Share / Copy |
| Meta (small) | Söhne | 13pt | 400 | 1.2 | 0pt | Timestamps, "Used 2.3k tokens" |
| Compose Placeholder | Söhne | 16pt | 400 | 1.4 | 0pt | "Message ChatGPT..." |
| Tab Label (rarely used) | Söhne | 11pt | 500 | 1.0 | 0.1pt | — |
| Link Text | Söhne | 16pt | 400 | 1.5 | 0pt | Blue `#2A7FFF`, underlined on hover |

### Principles
- **Document rhythm, not UI rhythm**: typography follows editorial conventions — body paragraphs at 1.5 line-height, markdown headings at progressively larger sizes
- **Weight concentrated at 400 / 500 / 600**: regular body, medium for chips/buttons, semibold for headings — no bold, no black, no thin
- **Söhne / Inter / SF Pro are all acceptable** — each renders a slightly different feel; the system should gracefully fall back in order
- **Markdown is first-class**: the assistant's response is rendered markdown (headings, lists, bold, italic, inline code, code blocks, tables, LaTeX math, links, images) — this is ChatGPT's response content
- **Dynamic Type first-class**: chat body, markdown headings scale; model chip, sidebar labels, timestamps stay fixed
- **Code fonts stay monospace**: inline code and code blocks always in Menlo/SF Mono/IBM Plex — never substitute

## 4. Component Stylings

### Buttons

**Send Button**
The single most recognizable ChatGPT component.
- Shape: Circle, 32pt diameter
- Background: `#0D0D0D` light / `#FFFFFF` dark (when enabled with typed text)
- Icon: SF Symbol `arrow.up` or `arrow.up.circle.fill`, 16pt, color inverse (`#FFFFFF` on black light / `#0D0D0D` on white dark)
- Position: right end of compose field
- Enabled when user has typed text; disabled gray `#CCCCCC` light / `#4D4D4D` dark when empty
- Pressed: scale 0.94 + haptic medium
- During generation: replaced by a square "stop" icon in same circle

**Voice Button (Microphone)**
- Shape: Circle, 32pt diameter (matches send button visual)
- Background: transparent with 1pt `#E5E5E5` border / 1pt `#424242` dark border
- Icon: SF Symbol `mic.fill` or `waveform`, 18pt, `#0D0D0D` light / `#ECECEC` dark
- Tap: enters full-screen voice mode
- Long-press: start recording; release to send audio message

**Action Icon Row (Compose bar, when empty)**
- Horizontal row of icons inside the compose field, appearing when compose is empty (replaces send button until user types)
- Icons (left to right): paperclip (attach), globe (web search toggle), microphone (voice)
- 20pt glyph, 40pt hit area, `#676767` / `#B4B4B4` dark
- Pressed: background shift to `#F0F0F0` / `#2F2F2F` dark (rounded 8pt)

**Regenerate / Copy / Thumbs Row (below assistant response)**
- Horizontal row of 4 icons: regenerate (arrow.triangle.2.circlepath), copy (doc.on.doc), thumbs-up (hand.thumbsup), thumbs-down (hand.thumbsdown)
- Each 20pt glyph in 32pt circular hit area
- Default: `#676767`
- Hover / active: background `#F0F0F0` circle appears, icon `#0D0D0D`
- Only visible on hover (desktop) or on tap of the message (mobile)

**Model Selector Chip (top-left of chat)**
- Shape: Pill, 500pt corner radius
- Background: transparent with 1pt `#E5E5E5` / `#424242` dark border (on hover/tap, becomes `#F0F0F0` fill)
- Leading: small model icon (abstract sparkle or geometric shape, 16pt)
- Text: "GPT-4o" / "GPT-4" / "GPT-5" / "o3" — Söhne 14pt w500
- Trailing chevron.down 12pt
- Padding: 6pt vertical, 10pt horizontal
- Tap: opens bottom sheet with model picker (list of available models + descriptions)

**Text Link Button ("Upgrade to Plus" etc.)**
- Color: `#2A7FFF` (link blue)
- Font: Söhne 14pt weight 500
- Optional underline on hover

### Conversation Atoms

**User Message Bubble**
- Shape: Rounded corners (18pt on top-right, 18pt top-left, 18pt bottom-left, 4pt bottom-right — "chat bubble" with flat pointing-corner on speaker side)
- Background: `#F7F7F8` light / `#2F2F2F` dark
- Text: Söhne 16pt w400 `#0D0D0D` / `#ECECEC`
- Padding: 10pt vertical, 14pt horizontal
- Align: right (trailing), max-width 80% of container width
- Attachment preview (if any): above the text, 72pt square tile with file icon + filename

**Assistant Message (no bubble)**
- Structure: plain markdown-rendered content flowing inline
- Leading small avatar icon (optional, 24pt — the ChatGPT sparkle logo)
- Text: Söhne 16pt w400 `#0D0D0D` / `#ECECEC`
- Line height: 1.5
- Align: left (leading), max-width: full container
- Below content: interaction row (regenerate / copy / thumbs)

**Inline Code Chip**
- Background: `#F0F0F0` / `#424242`
- Font: Menlo 14pt
- Padding: 2pt vertical, 4pt horizontal
- Corner radius: 4pt

**Code Block**
- Background: `#F7F7F8` / `#1E1E1E`
- Border: 1pt `#E5E5E5` / `#363636`
- Corner radius: 8pt
- Top strip (40pt tall): language label (e.g., "swift", "python") in Söhne 12pt w500 `#676767` + trailing "Copy" button
- Body: syntax-highlighted code in Menlo 13pt, scrollable horizontally
- Padding: 12pt

**Table Rendering**
- Full-width within message
- Cells: 1pt `#E5E5E5` borders all around
- Header row: bold Söhne 14pt w600, bg `#F7F7F8`
- Body rows: Söhne 14pt w400
- Padding: 8pt per cell

**LaTeX / Math Rendering**
- Inline: `$x^2 + y^2$` renders via MathJax/KaTeX, inline with text
- Block: `$$...$$` centered on own line, 120% font size

### Sidebar

**Conversation List Item**
- Height: 40pt
- Padding: 8pt vertical, 12pt horizontal
- Background: transparent; hover `#F0F0F0` / `#252525`; active `#ECECEC` / `#2F2F2F`
- Text: auto-generated conversation title (e.g., "React Hooks Explained") in Söhne 15pt w500 `#0D0D0D` / `#ECECEC`, 1-line clamp with ellipsis
- Trailing (on hover): ellipsis menu `⋯` — opens Rename / Share / Delete / Archive
- Long-press on mobile: opens same menu

**Sidebar Section Header**
- Text: "Today", "Yesterday", "Previous 7 Days", "Previous 30 Days", "June 2024" (month-grouped for older)
- Font: Söhne 12pt w500 `#676767` / `#B4B4B4`
- Padding: 16pt top, 4pt bottom, 12pt horizontal

**Sidebar Top Area**
- "New Chat" button at top — 44pt tall, full-width minus 12pt insets, rounded 8pt
- Leading icon `square.and.pencil` + label "New chat" Söhne 14pt w500
- Active: background `#0D0D0D` text `#FFFFFF` (or dark mode `#FFFFFF` text `#0D0D0D`)

**Sidebar Search**
- Below "New Chat" button: 36pt tall search field, 8pt rounded
- Placeholder "Search chats"

**Sidebar User Section (bottom)**
- User avatar (32pt circle) + name + Plus/Team badge + cog icon
- Tap: opens settings

### Top Nav (Chat View)

- Height: 52pt + safe area
- Background: transparent (or slightly tinted on scroll)
- Leading: hamburger / sidebar toggle (24pt) OR workspace avatar
- Center-leading: model selector chip (see above)
- Trailing: "New chat" icon (`square.and.pencil`), 24pt

### Input Fields

**Message Composer (docked bottom)**
- Background: `#FFFFFF` / `#212121` dark (same as canvas)
- Border: 1pt `#E5E5E5` / `#424242` top — just on top edge, or an entire rounded-rectangle container
- Padding: 8pt horizontal, 8pt vertical
- Shape: full-width rounded rectangle, 24pt corner radius (pill-like on iPhone), or full-bleed with just top border
- Auto-growing text area: min 48pt height, max 144pt (~6 lines) then scrolls
- Leading: `+` or paperclip icon for attachments (opens sheet: Photos, Camera, Files, Google Drive)
- Trailing: when empty — globe icon + microphone icon; when has text — send button

### Voice Mode (Full-screen)

- Full-screen overlay, transparent dimming on entry
- Center: pulsing blue gradient sphere (3B82F6 → 60A5FA → 93C5FD) about 280pt diameter
- Sphere animates organically (Perlin-noise-driven deformation + scale pulse)
- Waveform visualization overlay on sphere during user speech
- Background: the gradient canvas takes over the screen
- Controls: "Mute" toggle bottom-left, "End" (X) button bottom-right, "Swap" to change voice
- Assistant speech: subtle text overlay at bottom while speaking ("ChatGPT is speaking..." small white)
- End: taps X, returns to chat with a new entry "[voice conversation]" added to the history

### Distinctive Components

**Send Button (Black/White Circle)**
The signature — 32pt circle, black-on-white or white-on-black inverse, single up-arrow glyph. Universally recognizable.

**Voice Mode Sphere**
A mesmerizing full-screen blue gradient sphere that pulses and morphs as you speak. It's the product's most-recognized animated moment.

**Model Selector Chip**
"GPT-4o", "GPT-5" chip top-left — tap opens a sheet with all models.

**Regenerate + Feedback Icons**
After every assistant response: regenerate / copy / 👍 / 👎 — a 4-icon row that signals "this is an AI response, you can refine or rate it".

**Typing Indicator**
3 dots pulsing in sequence while the model is generating. Below the last message while streaming, then fades when text appears.

**Code Block with Copy**
Every fenced code block has a top strip with the language label + a "Copy" button on the right. Copy gives a brief "Copied!" toast.

**Attachment Preview in User Bubble**
When attaching a file/image, the attachment shows as a small tile above the text in the user bubble (72pt square with file icon or image thumbnail + truncated filename).

**Auto-titled Conversations**
Every new chat gets an auto-generated 3-5 word title after the first exchange ("React Hooks Explained", "Django ORM Help"), shown in the sidebar.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Message vertical gap: 16pt between turns
- User bubble max-width: 80% of container
- Assistant response: full-width
- Sidebar conversation item: 40pt tall, 12pt horizontal padding

### Grid & Container
- iPhone: full-bleed chat with 16pt horizontal insets; composer docks at bottom with safe-area padding
- iPad: split-view — sidebar (260pt) + chat (centered, max-width 780pt); very generous centering for readability
- Message bubbles never span full width — the 80% max keeps conversation visually anchored to side

### Whitespace Philosophy
- **Generous chat rhythm**: 16pt between turns, 1.5 line-height on body — a reading-first app
- **Minimal chrome**: only the model chip at top, the send button at bottom-right, and the sidebar when open
- **Centered at rest**: on an empty new chat, the compose field sits slightly below center with a small model-name + greeting ("Ready when you are" or "How can I help you today?")
- **Sidebar is secondary**: slides in but not always visible; the chat is primary

### Border Radius Scale
- Square (0pt): Plain text (body, assistant response)
- Subtle (4pt): Inline code chips, inline attachment tiles
- Standard (8pt): Code blocks, tables, sidebar "New chat" button
- Comfortable (12pt): Sheets, model picker
- Bubble Corner (18pt on 3 corners + 4pt on pointer corner): User message bubble — asymmetric "chat bubble" rounding
- Large (24pt): Compose field wrapper on iPhone (nearly pill)
- Circle (50%): Send button, voice button, user avatar

## 6. Depth & Elevation

ChatGPT is radically flat. The only shadows are on ephemeral UI.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Messages, sidebar rows, code blocks, canvas |
| Floating (Level 1) | `rgba(0,0,0,0.08) 0 4px 12px` | Model picker sheet, share sheet, attachment sheet |
| Sheet (Level 2) | `rgba(0,0,0,0.16) 0 -4px 24px` | Bottom sheets (settings, model picker) |
| Modal Overlay | `rgba(0,0,0,0.5)` | Full-screen modals, voice mode backdrop |
| Voice Mode Sphere | Radial gradient + glow `rgba(59,130,246,0.4) blur 40` | The pulsing sphere glow |

**Shadow Philosophy**: ChatGPT treats shadows as functional, not decorative — they appear only where the "this is floating above, tap outside to dismiss" signal is needed. The chat itself is absolutely flat. Code blocks and tables are bordered (1pt), not shadowed. The voice-mode sphere is the dramatic exception — it glows with a radial blue gradient that extends beyond its edge.

### Motion
- **Message send**: user bubble slides up from bottom with 250ms ease-out; send button pulses briefly (scale 0.9 → 1.0)
- **Typing indicator**: 3 dots pulse in sequence — each dot opacity 0.3 ↔ 1.0 over 600ms, staggered 200ms
- **Assistant streaming**: each token appears inline as it arrives (no animation — just character-by-character append)
- **Regenerate tap**: assistant response fades out 200ms, new response streams in
- **Voice mode entry**: scale from 0 → 1.0 with spring, opacity 0 → 1, 400ms; sphere starts pulsing immediately
- **Voice mode sphere**: continuous organic pulse — scale 0.95 ↔ 1.05 over 2s ease-in-out, with Perlin-noise deformation on SVG path
- **Sidebar slide**: edge-swipe 1:1 tracking; spring 250ms damping 0.8 on release
- **New chat transition**: existing chat fades out 150ms, empty state fades in 150ms
- **Copy button**: tap shows brief "Copied!" pill that fades in/out over 1s
- **Thumbs feedback**: tap fills icon + brief scale 1.0 → 1.15 → 1.0 spring + soft haptic
- **Haptics**: soft on send / copy / thumbs; medium on voice mode entry; success on long action completion

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as canvas light / charcoal-black (`#212121`) dark — NOT true black
- Make the send button a circle — black-on-white light, white-on-black dark, 32pt with up-arrow
- Keep the UI monochromatic — no brand accent beyond subtle blue (`#2A7FFF`) on links
- Give user messages a soft-gray bubble (`#F7F7F8`) — assistant messages get NO bubble
- Render markdown in assistant responses — code blocks, tables, LaTeX, headings all rendered cleanly
- Use Söhne / Inter / SF Pro in that fallback order
- Use Menlo / SF Mono for inline code chips and code blocks
- Place model selector chip top-left — pill with model name + leading icon + chevron
- Show thumbs up/down + regenerate + copy below every assistant response
- Use asymmetric bubble corners on user messages (18pt / 18pt / 18pt / 4pt — flat pointer corner on right)
- Make voice mode a full-screen pulsing blue gradient sphere — the most distinctive moment
- Auto-generate 3-5 word titles for conversations in the sidebar
- Group sidebar chats by time: "Today / Yesterday / Previous 7 Days / Previous 30 Days / Month Year"

### Don't
- Don't use bubbles around assistant responses — plain inline text flow is the rule
- Don't use ChatGPT Green (`#10A37F`) — it's been mostly retired; keep the UI monochromatic
- Don't make the send button square or rectangular — it's a circle
- Don't use heavy shadows on content — ChatGPT is flat; shadows only on floating ephemeral UI
- Don't render markdown naively — code blocks need syntax highlighting + copy button
- Don't hardcode only SF Pro — fall back gracefully to Söhne > Inter > SF Pro
- Don't make the canvas pure black (`#000`) — use `#212121` on dark
- Don't show all sidebar chats as a flat list — group by time
- Don't animate text appearing — stream it character-by-character without motion
- Don't use a generic chat-app avatar placeholder — ChatGPT uses a small sparkle logo or none at all
- Don't omit the 4-icon feedback row (regenerate / copy / 👍 / 👎) — it's the AI-response signature
- Don't add a brand color to the compose field — pure monochrome

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Sidebar at 80% width when open; compose max-width 90% |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | User bubble max at 70% (more margin visible) |
| iPad (portrait) | 768pt | 2-column: sidebar (260pt) + chat (centered max 680pt) |
| iPad (landscape) | 1024pt+ | Same 2-column; chat max 780pt |

### Dynamic Type
- Chat body, markdown H1/H2/H3, inline text: full scale
- Model chip, sidebar titles, section headers, timestamps: FIXED
- Code blocks: scale to XL (but stay monospace)

### Orientation
- iPhone: rotates freely — chat works well in landscape too
- Voice mode: portrait-locked (the full-screen sphere is designed for portrait)

### Touch Targets
- Send button: 32pt, 44pt hit area
- Voice button: 32pt, 44pt hit area
- Icons in compose row: 40pt hit
- Regenerate / thumbs: 32pt hit (tight — consider 44pt minimum for accessibility)
- Model chip: 36pt tall, 44pt effective
- Sidebar row: 40pt, full-row tap

### Safe Area Handling
- Top: top nav respects safe area and Dynamic Island
- Bottom: compose bar respects home indicator with extra padding
- Keyboard: chat scrolls above keyboard; compose bar rises with keyboard
- Voice mode: ignores safe area (full-bleed sphere)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#212121`
- Text primary: `#0D0D0D` / `#ECECEC` dark
- Text secondary: `#676767` / `#B4B4B4` dark
- User bubble (light): `#F7F7F8`
- User bubble (dark): `#2F2F2F`
- Send button: `#0D0D0D` light / `#FFFFFF` dark
- Send icon: inverse of circle
- Inline code bg: `#F0F0F0` / `#424242`
- Code block bg: `#F7F7F8` / `#1E1E1E`
- Link blue: `#2A7FFF`
- Sidebar bg (light): `#F9F9F9`
- Sidebar bg (dark): `#181818`
- Divider: `#E5E5E5` / `#424242`
- Legacy ChatGPT green (retired mostly): `#10A37F`
- Voice mode sphere gradient: `#3B82F6` → `#60A5FA` → `#93C5FD`

### Example Component Prompts
- "Build the ChatGPT send button in SwiftUI: 32pt circle, `#0D0D0D` background (or `#FFFFFF` on dark mode), SF Symbol `arrow.up` 16pt weight bold in contrasting inverse color (`#FFFFFF` light, `#0D0D0D` dark). Position: right end of compose field. Enabled only when text has been typed — disabled state is `#CCCCCC` light / `#4D4D4D` dark with same icon. Pressed: scale 0.94 + medium haptic. Replace with a square `stop.fill` icon during streaming."

- "Create a ChatGPT user message bubble: right-aligned, max-width 80% of container. Background `#F7F7F8` light / `#2F2F2F` dark. Asymmetric corner radii: top-left 18pt, top-right 18pt, bottom-left 18pt, bottom-right 4pt (the 'pointer' corner). Padding 10pt vertical 14pt horizontal. Text Söhne 16pt w400 `#0D0D0D` / `#ECECEC`. If attachment: 72pt square thumbnail above text with file icon + truncated filename in 12pt `#676767`."

- "Design the ChatGPT assistant response: leading optional 24pt sparkle-logo icon, then full-width markdown-rendered content in Söhne 16pt w400 line-height 1.5 `#0D0D0D` / `#ECECEC`. NO bubble — just plain text flowing. Markdown headings at H1 24pt / H2 20pt / H3 17pt, all w600. Inline code `\`code\`` in Menlo 14pt with `#F0F0F0` bg and 4pt radius. Code blocks: `#F7F7F8` bg, 1pt `#E5E5E5` border, 8pt radius, top strip with language label in Söhne 12pt w500 `#676767` + right-aligned 'Copy' button. Below the response: 4-icon row — regenerate (`arrow.triangle.2.circlepath`), copy (`doc.on.doc`), thumbsup, thumbsdown — each 20pt in 32pt hit area, `#676767` default."

- "Render the ChatGPT model selector chip: top-left of chat, pill shape 500pt radius, 1pt `#E5E5E5` border, transparent fill (or `#F0F0F0` on press). Leading 16pt model icon (abstract sparkle shape), 'GPT-4o' label in Söhne 14pt w500 `#0D0D0D`, trailing chevron.down 12pt. Padding 6pt vertical 10pt horizontal. Tap: presents bottom sheet with model picker listing GPT-4o / GPT-4 / GPT-3.5 / etc with descriptions."

- "Build the ChatGPT compose field: docked at bottom, `#FFFFFF` / `#212121` dark background, rounded rectangle container with 24pt corner radius. Auto-growing text area: min 48pt, max ~144pt then scroll. Placeholder 'Message ChatGPT…' in Söhne 16pt w400 `#676767`. Leading `+` icon button for attachments. Trailing: when empty, show two icons globe (web) + microphone, both 20pt `#676767` in 40pt hit areas; when text entered, replace with the black 32pt send button circle with up-arrow. Text input in Söhne 16pt w400 `#0D0D0D`."

- "Create ChatGPT voice mode: full-screen takeover, background fades to black. Center: ~280pt diameter animated sphere with radial gradient `#3B82F6` → `#60A5FA` → `#93C5FD`, pulsing continuously (scale 0.95 ↔ 1.05 over 2s ease-in-out) + Perlin-noise deformation on the SVG circle path. Sphere has a glow: `rgba(59,130,246,0.4) 0 0 80pt`. Bottom-center: waveform visualization while user speaks. Bottom-left: 'Mute' button. Bottom-right: 'End' (X) button — 44pt circular, white on semi-transparent black. While assistant speaks: small white text 'ChatGPT is speaking…' above controls. Entrance: scale 0 → 1 + opacity 0 → 1 over 400ms spring; haptic medium on entry."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) in light, charcoal (`#212121`) dark — NOT true black
2. Send button is a 32pt circle, black-on-white light / white-on-black dark — single up-arrow glyph
3. User messages get a soft-gray bubble with asymmetric corners (18/18/18/4); assistant messages get NO bubble
4. Typography is Söhne / Inter / SF Pro fallback order; code always in Menlo / SF Mono
5. UI is monochromatic — the only accent is subtle blue `#2A7FFF` on links; no brand green, no colorful chrome
6. Markdown in assistant responses is fully rendered — headings, code blocks with syntax + copy button, tables, LaTeX
7. Below every assistant response: 4 icons — regenerate / copy / thumbs-up / thumbs-down
8. Model selector chip top-left (pill with "GPT-4o" + chevron) is a canonical UI element
9. Voice mode is a full-screen blue gradient sphere that pulses organically — the dramatic flagship moment
10. Sidebar groups conversations by time: Today / Yesterday / Previous 7 Days / Previous 30 Days / Month Year
