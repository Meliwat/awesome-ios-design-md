# Design System Inspiration of Claude (iOS)

## 1. Visual Theme & Atmosphere

Claude's iOS app feels like an AI assistant rendered on a piece of warm paper. Anthropic deliberately rejected the cold blue-grey aesthetic that defined ChatGPT's first wave of imitators and instead chose a sun-warmed cream canvas (`#F8F4ED`), inky near-black type, and a single signature accent — Claude Orange (`#D97757`) — that appears on the send button, the active state of the model picker, and the asterisk-star logomark. The result is a chat surface that reads as humanist before it reads as technological: the AI doesn't shout at you in neon; it sits next to you on the same paper, in the same handwriting.

The orange itself is famously specific — it's a desaturated, slightly clay-toned terracotta, not a vivid tech orange. It carries the brand without dominating the screen because almost everything else is type and surface. Anthropic's design language is restraint: there are no gradients, no glass blurs over the chat content, no glow halos on buttons. The signature visual is text quality — long, beautifully typeset messages flowing across the cream canvas with serif headings inside the assistant's responses, paired with the artifact tray that slides out from the right when Claude renders code, a document, or a visualization.

Typography is the central design system. Anthropic uses Tiempos (a serif by Klim Type Foundry) for assistant message body text — the choice of a serif inside an AI app is itself a statement, because every other chat app uses sans for the assistant's responses. Headings within assistant messages are in Tiempos Headline; body is Tiempos Text; user messages and UI chrome are in Styrene (Klim's geometric humanist sans) or Inter as substitute. The voice the type carries is editorial-essayist: Claude reads like an essayist, not a chatbot, and the typography is the manifest of that intent.

**Key Characteristics:**
- Cream paper canvas (`#F8F4ED`) — the Claude "paper" — and a slightly whiter `#FBF9F4` for input fields and elevated surfaces
- Claude Orange (`#D97757`) — desaturated terracotta — for the send button, active model chip, and the asterisk-star logomark
- Tiempos serif for assistant message body (the central typographic choice); Styrene/Inter sans for user messages and UI chrome
- Asterisk-star logomark — a 6-point asterisk-meets-star glyph in Claude Orange — appearing on the avatar of every Claude message
- Artifact tray that slides out from the right (on iPad) or expands as a full-screen card (on iPhone) when Claude generates code or a document
- Model picker chip at the top: "Claude Opus 4", "Claude Sonnet 4.5", etc., with a small orange dot when the active model is Opus
- File and image attachments via a paperclip-plus button beside the input
- Streaming response cursor — a small blinking orange caret that animates while Claude is generating
- Subtle, paper-warm shadows (`rgba(40, 30, 20, 0.06)`) — never blue-grey
- Bottom safe-area pinned input bar with auto-grow textarea, model chip, paperclip, and the orange send circle

## 2. Color Palette & Roles

### Primary
- **Claude Orange** (`#D97757`): The signature accent — Send button fill, asterisk-star logomark, active state of model picker, streaming cursor, link color in assistant messages.
- **Orange Pressed** (`#BE6242`): Pressed state on the Send button.
- **Orange Soft** (`#F2DDD0`): Background of the active model chip, the "thinking…" indicator soft fill.

### Canvas (the Claude Paper)
- **Cream Paper** (`#F8F4ED`): The primary canvas — every chat screen background by default. The most recognizable Claude surface treatment.
- **Paper White** (`#FBF9F4`): Slightly elevated surfaces — input field background, code block background.
- **Surface Warm 1** (`#F0EAE0`): Section dividers, settings backgrounds, soft callouts.
- **Surface Warm 2** (`#E8E0D2`): Pressed states, hover/touch chip fills.
- **Divider Sand** (`#DDD2BD`): 0.5pt hairline row dividers on settings.

### Text
- **Ink** (`#2D2520`): Primary text — assistant message body, user message body, screen titles. Warm near-black, not pure.
- **Graphite Warm** (`#5A4F44`): Secondary text — timestamps, "Claude" / "You" sender labels, metadata.
- **Stone Warm** (`#8A7E72`): Tertiary text — placeholder, helper, "Press enter to send" hint.
- **Bone Warm** (`#B5AB9E`): Disabled labels, very-secondary metadata.

### Code & Syntax (inside code blocks and artifacts)
- **Code Background** (`#1F1B16`): Dark warm canvas inside fenced code blocks — slightly orange-tinted, never blue-tinted
- **Code Text** (`#E8E0D2`): Default code text
- **Syntax Keyword** (`#D97757`): Keywords like `function`, `const`, `return` — Claude Orange
- **Syntax String** (`#7FB069`): Strings in code — sage green
- **Syntax Number** (`#E8B96F`): Numbers — warm gold
- **Syntax Comment** (`#8A7E72`): Comments — warm grey
- **Syntax Function** (`#9DA4F2`): Function names — soft periwinkle

### Semantic
- **Success Green** (`#6B9D5E`): "Copied" toast, "Saved" confirmations — sage, not lime
- **Warning Amber** (`#D49952`): "Approaching context limit" — warm amber
- **Error Red** (`#C16654`): Form errors, "Failed to send" — terracotta-toned red, not pure
- **Info Slate** (`#5A6273`): Rarely used — "Beta" badges, "What's new" callouts

### Dark Mode
Claude's dark mode launched alongside the iOS app. It is warm-dark, preserving the paper feel.
- **Dark Canvas** (`#1F1B16`): Primary dark background — warm near-black, slightly orange-undertoned.
- **Dark Surface 1** (`#2A2520`): Cards, sheets, input field fill.
- **Dark Surface 2** (`#3A332C`): Pressed states, chip fills, hover.
- **Dark Divider** (`#3A332C`): Hairlines.
- **Dark Text Primary** (`#E8E0D2`): Inverted Ink — warm cream type.
- **Dark Text Secondary** (`#B5AB9E`): Inverted Graphite Warm.
- **Claude Orange (dark)** (`#D97757`): Unchanged — the orange reads well on warm dark canvas.
- **Orange Soft (dark)** (`#4A352A`): Dark mode active-chip background.

## 3. Typography Rules

### Font Family
- **Primary (assistant body)**: `Tiempos Text` — proprietary serif by Klim Type Foundry, the central typographic choice of the Claude brand
- **Display (headings inside assistant messages)**: `Tiempos Headline` — the same family at heavier display weights
- **Secondary (UI chrome, user messages)**: `Styrene A` or `Styrene B` (Klim) — geometric humanist sans
- **Weights available**: Tiempos Regular (400), Medium (500), Semibold (600), Bold (700); Styrene Regular (400), Medium (500), Bold (700)
- **Fallback stack for serif**: `'Source Serif Pro', 'Georgia', 'Times New Roman', serif` — Source Serif is the closest Google Fonts substitute
- **Fallback stack for sans**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', sans-serif`
- **Web/marketing Google Fonts substitute**: `Source Serif 4` for the serif and `Inter` for the sans — both ship on most platforms

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display (Settings, About) | Tiempos Headline | 32pt | 600 | 1.2 | -0.4pt | "Settings", "About Claude" |
| Conversation Title | Styrene A | 18pt | 600 | 1.25 | -0.1pt | Auto-generated chat title at the top of conversation |
| H1 (Assistant) | Tiempos Headline | 24pt | 600 | 1.3 | -0.2pt | Top-level headings inside Claude's response |
| H2 (Assistant) | Tiempos Headline | 20pt | 600 | 1.3 | -0.1pt | Section headings inside response |
| H3 (Assistant) | Tiempos Headline | 17pt | 600 | 1.35 | 0pt | Subsection in response |
| Assistant Body | Tiempos Text | 16pt | 400 | 1.55 | 0pt | The most-rendered text in the app — Claude's prose |
| Assistant Body Italic | Tiempos Text Italic | 16pt | 400 | 1.55 | 0pt | Emphasis in Claude's responses |
| Assistant Body Bold | Tiempos Text | 16pt | 600 | 1.55 | 0pt | Emphasis in Claude's responses |
| User Message | Styrene A | 16pt | 400 | 1.5 | 0pt | What the user typed |
| User Message Background | n/a | n/a | n/a | n/a | n/a | The user message sits in a Surface Warm 1 pill, not a chat bubble |
| Inline Code | JetBrains Mono | 14pt | 500 | 1.4 | 0pt | `inline code` inside prose |
| Code Block | JetBrains Mono | 14pt | 400 | 1.5 | 0pt | Fenced code blocks |
| List Item | Tiempos Text | 16pt | 400 | 1.5 | 0pt | Bullet / numbered list items in Claude's response |
| Blockquote | Tiempos Text Italic | 16pt | 400 | 1.5 | 0pt | Block-quoted text in Claude's response |
| Input Placeholder | Styrene A | 16pt | 400 | 1.4 | 0pt | "Reply to Claude…" |
| Button (Send) | n/a (icon) | n/a | n/a | n/a | n/a | The Send button is icon-only |
| Button (Action) | Styrene A | 15pt | 600 | 1.0 | 0pt | "Continue", "Reset chat" |
| Model Chip | Styrene A | 13pt | 500 | 1.0 | 0pt | "Claude Opus 4" in the picker |
| Metadata | Styrene A | 12pt | 400 | 1.3 | 0pt | Timestamps, "Claude Opus 4 · 2:14 PM" |
| Caption | Styrene A | 11pt | 400 | 1.3 | 0pt | "Press ⏎ to send" |

### Principles
- **Serif body for Claude, sans for the user**: This single decision is the brand. When Claude replies in Tiempos and you reply in Styrene, the conversation has a typographic role-asymmetry that no other AI app reproduces.
- **Loose line-height on assistant body**: 1.55x at 16pt — Claude's responses are essays, not Slack messages. Generous leading is non-negotiable.
- **Warm ink, never pure black**: Body text sits at `#2D2520`, not `#000000` — alongside the cream canvas, this is what makes the chat feel like print.
- **Markdown rendering is the design**: Headings, lists, quotes, code blocks, tables, and bold/italic all render their distinct typographic identity inside Claude's responses. The chat surface is a long-form document, not a string of bubbles.
- **Dynamic Type respected on all reading text**: Assistant body, headings, user messages all scale; the model chip, timestamps, and the send icon are fixed (layout-sensitive).
- **Slight optical sizing**: Tiempos Headline is used at 17pt+ for headings; Tiempos Text at 16pt for body. Don't mix them at the same size.

## 4. Component Stylings

### Buttons

**Send Circle (the orange icon-only send)**
- Diameter: 40pt
- Background: `#D97757` (Claude Orange) when input has text; `#E8E0D2` (Surface Warm 2) when input is empty
- Icon: 16pt arrow-up glyph, color `#FBF9F4` (Paper White) when active, `#8A7E72` (Stone) when disabled
- Pressed: scale 0.94, background `#BE6242`
- Haptic: `.impactOccurred(.medium)` on send
- Sits trailing the input field, vertically centered

**Action Button (Continue / Reset / Regenerate)**
- Background: `#FBF9F4` (Paper White)
- Border: 1pt `#DDD2BD` (Divider Sand)
- Text: `#2D2520` (Ink), Styrene A 15pt weight 600
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 10pt
- Pressed: background `#F0EAE0`
- These appear at the end of an assistant message as suggested next actions

**Primary Pill (Continue conversation, sign-up CTAs)**
- Background: `#2D2520` (Ink — warm black)
- Text: `#FBF9F4` (Paper White)
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 24pt (capsule)
- Pressed: scale 0.97

**Copy Button (on code blocks and messages)**
- Floats top-right inside code blocks and on the right edge of long assistant messages on hover/tap
- 32pt × 32pt button, `rgba(255, 255, 255, 0.05)` background inside dark code blocks; `#F0EAE0` on cream
- Icon: `doc.on.doc` 14pt
- On press: icon morphs to `checkmark` for 1200ms with a small "Copied" toast slide-up

### Cards & Containers

**User Message Pill (the user-said-it container)**
- The user's message sits in a soft pill, NOT a chat bubble with a tail
- Background: `#F0EAE0` (Surface Warm 1)
- Padding: 12pt vertical, 16pt horizontal
- Corner radius: 18pt
- Max width: 80% of screen width
- Aligned right (or right-of-center on iPad)
- Text: Styrene A 16pt Regular `#2D2520`
- No metadata strip — the timestamp shows on tap

**Assistant Message Block (the conversation hero)**
- The assistant's message is a full-width flow of typography — NO pill, NO bubble, NO container background
- 16pt avatar (asterisk-star in Claude Orange) at the leading edge, 12pt before the text
- "Claude" sender label in Styrene A 13pt Medium `#5A4F44`, with the model name in 13pt Regular `#8A7E72` next to it
- The body content renders as a long-form document: Tiempos Text 16pt with full markdown — headings (Tiempos Headline 17-24pt), lists, code, quotes, all distinct typographic identities
- The full assistant message can run as tall as needed; the conversation scrolls naturally
- A faint 1pt `#F0EAE0` divider sits between turns, 32pt above and below

**Code Block (inside assistant messages)**
- Background: `#1F1B16` (Code Background — warm dark, never blue)
- Border: 0.5pt `#3A332C` in dark theme; 1pt `#DDD2BD` on cream
- Corner radius: 12pt
- Padding: 16pt all around
- Header strip (top of block): language name in JetBrains Mono 11pt `#8A7E72` left-aligned + Copy button right-aligned, 1pt `#3A332C` bottom divider
- Code text: JetBrains Mono 14pt Regular `#E8E0D2` on warm dark
- Syntax highlighting uses the warm palette (orange keywords, sage strings, gold numbers, periwinkle functions)
- Horizontal scroll for long lines, no wrap

**Inline Code**
- Background: `#F0EAE0` on cream; `#3A332C` on dark
- Padding: 1pt vertical, 4pt horizontal
- Corner radius: 4pt
- Font: JetBrains Mono 14pt Medium `#2D2520`

**Artifact Card (when Claude generates a document, code file, chart)**
- On iPhone: appears as a card inset in the message flow — 12pt corner radius, `#FBF9F4` Paper White background, 1pt `#DDD2BD` border, padding 16pt
- Tap to open: presents as a full-screen modal with the artifact rendered + a "Continue editing" button
- Top of card: artifact title in Tiempos Headline 17pt Medium + a small icon indicating type (document / code / chart)
- Body of card: a preview — first ~6 lines of code, or a thumbnail of the rendered document
- Footer: "Open" button on the right
- On iPad: opens as a side-pane that takes 40% of the screen width, leaving the chat at 60%; the divider between is draggable

**Conversation Card (in the sidebar / history list)**
- Each row: 64pt tall, full width
- 14pt vertical / 16pt horizontal padding
- First line: title in Styrene A 15pt Medium `#2D2520`, 1-line truncate
- Second line: timestamp in Styrene A 12pt Regular `#8A7E72`
- Selected state: `#F0EAE0` background, 1pt `#DDD2BD` left border (3pt wide indicator at the leading edge)
- Hover/press: subtle `#F0EAE0` fill

### Navigation

**Top Header Bar**
- Height: 52pt (slim header)
- Background: `#F8F4ED` (canvas), no blur — content scrolls naturally underneath
- Leading: sidebar toggle button (3-line hamburger glyph, `#2D2520`)
- Title: the conversation title in Styrene A 17pt Semibold `#2D2520`, centered or left-of-center
- Trailing: "..." overflow menu (model picker, settings, share)

**Model Picker Chip (at the top of every conversation)**
- Sits beneath the header, 8pt below
- A small pill: `#F0EAE0` (Surface Warm 1) background, 6pt vertical / 12pt horizontal padding, 16pt corner radius
- Content: 14pt asterisk-star glyph in `#D97757` Claude Orange + "Claude Opus 4" in Styrene A 13pt Medium `#2D2520` + downward chevron 10pt `#8A7E72`
- Tap opens a sheet with model options; the active model has a `#D97757` orange dot to its left

**Sidebar (Conversation History)**
- Slides in from the left on iPhone (full-screen modal); persistent on iPad (260pt width)
- Background: `#F0EAE0` (Surface Warm 1)
- Top: "+ New chat" button — full width, `#FBF9F4` Paper White background, 1pt `#DDD2BD` border, 12pt corner radius, 12pt vertical padding, leading 16pt plus icon + Styrene A 15pt Medium label
- Below: list of conversation rows grouped by date — "Today", "Yesterday", "Last 7 days", etc.
- Group headers: Styrene A 11pt Bold UPPERCASE `#8A7E72`, 0.4pt letter spacing
- Each row: see Conversation Card above

**No Bottom Tab Bar**
- Claude does not use a bottom tab bar — the app is a single-purpose chat. Navigation is via sidebar.

### Input Fields

**Chat Input (the most-used input — auto-growing textarea)**
- Width: full minus 32pt margins
- Background: `#FBF9F4` (Paper White)
- Border: 1pt `#DDD2BD` default; 1.5pt `#D97757` focused (orange ring)
- Corner radius: 20pt (capsule when single-line, softer when grown to multi-line)
- Min height: 52pt; auto-grow up to 200pt before becoming scrollable
- Padding: 14pt vertical, 18pt left, 56pt right (to make room for the send button)
- Placeholder: "Reply to Claude…" in Styrene A 16pt Regular `#8A7E72`
- Text: Styrene A 16pt Regular `#2D2520`
- Trailing: paperclip-plus button (28pt circle, transparent, `#5A4F44` icon) leading the send button
- Send circle: 40pt circle, Claude Orange when active, see Send Circle spec
- Below the input: a thin row of action chips ("Code interpreter", "Web search", "Use my docs") in Styrene A 11pt Medium pills, only visible when input is empty

**Settings Text Field**
- Background: `#FBF9F4`
- Border: 1pt `#DDD2BD` default, 1.5pt `#2D2520` focused
- Corner radius: 10pt
- Height: 48pt
- Padding: 12pt vertical, 14pt horizontal
- Label: above the field in Styrene A 13pt Medium `#5A4F44`

### Distinctive Components

**Asterisk-Star Logomark (Claude's avatar)**
- A 6-point asterisk-meets-star symbol — Anthropic's Claude logomark
- Rendered as a single shape: 6 narrow petals radiating from the center, slightly rounded tips, evoking a star/sparkle/asterisk hybrid
- Render: `#D97757` Claude Orange on cream; `#D97757` on dark (it pops on warm dark too)
- Sizes: 14pt in the model chip, 18pt on assistant message avatars, 28pt+ in onboarding hero, 56pt on splash

**Streaming Response Cursor**
- A small orange caret that appears at the trailing edge of Claude's response while the model is generating
- 8pt wide, 18pt tall, `#D97757`
- Animation: blinks at 600ms interval (300ms on / 300ms off); pauses while a new token arrives, then resumes
- Disappears when the response is complete; replaced by the action buttons (Continue, Regenerate, Copy)

**"Thinking…" Indicator (extended thinking models)**
- Appears when Opus or Sonnet with extended thinking is generating before the visible response
- A small pill: `#F2DDD0` (Orange Soft) background, 6pt vertical / 12pt horizontal padding, 12pt corner radius
- Content: 14pt asterisk-star pulsing + "Thinking…" in Styrene A 13pt Medium `#5A4F44`
- The asterisk pulses scale 1.0 → 1.15 → 1.0 every 1200ms while visible
- Optional: a subtle progress indicator on long thinking — "Thought for 14s"

**Model Picker Sheet**
- A bottom sheet, 24pt top corners, `#FBF9F4` Paper White background
- List of models — each row is 64pt tall with:
  - 24pt asterisk-star icon at the leading edge (Claude Orange for active, `#B5AB9E` muted for unavailable)
  - Model name in Styrene A 16pt Medium `#2D2520`
  - Sub-line: "Most intelligent" / "Fast and capable" / "Quickest" in Styrene A 13pt Regular `#5A4F44`
  - A `#D97757` orange dot at the trailing edge when this is the currently selected model
- 1pt `#F0EAE0` divider between rows
- Tap selects + dismisses the sheet with a `.selectionChanged()` haptic

**Onboarding Hero**
- Full-screen `#F8F4ED` cream canvas
- 80pt asterisk-star logomark centered, `#D97757`
- "Hi, I'm Claude" in Tiempos Headline 32pt Semibold `#2D2520` centered, 24pt below the logomark
- Subline: "An AI assistant by Anthropic." in Tiempos Text 16pt `#5A4F44` centered
- Primary CTA: "Start a conversation" — Ink Pill at the bottom

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 20pt on iPhone, 32pt on iPad
- Vertical gap between conversation turns: 32pt (with the faint divider centered)
- Generous internal padding: 16pt inside code blocks and artifact cards

### Grid & Container
- Content width: full minus 20pt margins on iPhone
- On iPad: max content width 720pt centered with side margins, so the prose stays readable
- Sidebar: 260pt fixed width on iPad; full-screen modal on iPhone
- Artifact pane on iPad: 40% screen width by default, draggable to 30-60%

### Whitespace Philosophy
- **The chat is a long-form essay**: Claude's responses can be hundreds of lines — the spacing has to accommodate reading, not scanning
- **Generous between turns**: 32pt gap with a faint divider; user pills and assistant flows are visually distinct
- **Tight inside the assistant message**: standard 1.55x line-height inside Tiempos body; headings get +12pt above and +4pt below; lists get 8pt between items
- **Margin-driven, not divider-driven**: Claude rarely uses heavy dividers; whitespace and type-weight differentiate sections

### Border Radius Scale
- Square (0pt): rarely used — only on hairline rules
- Small (4pt): inline code, tags
- Medium (10pt): settings rows, action buttons, code-block badges
- Standard (12pt): code blocks, artifact cards, "+ New chat" button
- Soft (16pt): model chip
- Large (18pt): user message pills
- XL (20pt): chat input field
- Capsule (24pt): primary pills, CTAs
- Sheet (24pt top corners): bottom sheets, modals
- Circle (50%): send button, asterisk-star avatar

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, assistant message blocks, dividers |
| Subtle (Level 1) | `rgba(40, 30, 20, 0.04) 0 1px 2px` | User message pills, action buttons on hover |
| Card (Level 2) | `rgba(40, 30, 20, 0.06) 0 2px 8px` | Artifact cards, code blocks (light theme) |
| Floating (Level 3) | `rgba(40, 30, 20, 0.10) 0 4px 16px` | Model picker chip on press, sidebar shadow |
| Overlay (Level 4) | `rgba(40, 30, 20, 0.14) 0 8px 24px` | Bottom sheets, model picker sheet |
| Sheet (Level 5) | `rgba(40, 30, 20, 0.18) 0 -12px 32px` | Full-screen modals (settings, artifact viewer) |

**Shadow Philosophy**: Claude's shadows use a warm base color (`rgba(40, 30, 20, x)`) so they harmonize with the cream canvas. The app is largely flat — the conversation surface is intentionally without elevation because the typography is the visual interest. Only the artifact cards, code blocks, and sheets get any shadow at all; everything else is differentiated by surface tone (`#F8F4ED` vs `#FBF9F4` vs `#F0EAE0`).

### Motion
- **Send tap**: 40pt circle scales 1.0 → 0.94 → 1.0 over 200ms, `.impactOccurred(.medium)` haptic, then the user message pill slides up from the input area in 300ms ease-out
- **Streaming text appearance**: tokens animate in with a 80ms fade-in per word — no typewriter character-by-character; word-by-word for readability
- **Streaming cursor blink**: 600ms interval (300ms on / 300ms off); pauses during token arrival
- **Artifact card expand**: shared-element 400ms spring — the inline card morphs into the full-screen modal (or side-pane on iPad)
- **Model picker open**: bottom sheet rises 300ms ease-out; the active model row is highlighted on entry
- **"Thinking…" pulse**: asterisk-star scales 1.0 → 1.15 → 1.0 every 1200ms; cubic-bezier ease
- **New chat reveal**: when "+ New chat" is tapped, the conversation list collapses left and the empty-state onboarding fades in over 250ms
- **Copy confirmation**: button icon morphs `doc.on.doc` → `checkmark` over 150ms; "Copied" toast slides up from the bottom over 200ms and dismisses after 1200ms
- **Sidebar slide (iPhone)**: 350ms ease with a 0 → 1 dim overlay on the chat content; swipe-to-dismiss responsive

## 7. Do's and Don'ts

### Do
- Use the cream paper canvas (`#F8F4ED`) on every chat screen — it's the most recognizable Claude surface
- Render Claude's responses in Tiempos serif and the user's messages in Styrene/Inter sans — the typographic role-asymmetry is the brand
- Reserve Claude Orange (`#D97757`) for the Send button, active model chip, asterisk-star logomark, streaming cursor, and links inside responses
- Render the asterisk-star logomark in Claude Orange — never in any other color
- Make assistant messages flat — no bubble container; just type flowing across the canvas with a 16pt logomark avatar leading
- Use the user message pill (Surface Warm 1) — a soft pill, NOT a bubble with a tail
- Render code blocks on warm dark `#1F1B16` — never on blue-tinted dark
- Use the warm syntax palette — orange keywords, sage strings, gold numbers, periwinkle functions
- Use 1.55x line-height on assistant body text — Claude is essayist, generous leading is non-negotiable
- Stream responses word-by-word, not character-by-character — readability over typewriter drama
- Show the orange streaming cursor at the trailing edge of Claude's text while generating
- Use Ink `#2D2520` for primary text — never `#000000`; the warmth is the brand

### Don't
- Don't use a chat bubble with a tail for either the user or the assistant — Claude's chat is flatter and more documentary
- Don't render Claude's responses in a sans-serif font — Tiempos is the brand's most important typographic decision
- Don't use Claude Orange for body text, headings, or section dividers — it's a verb, not a color
- Don't replace the asterisk-star logomark with a generic AI sparkle or a stylized "C" — the 6-point asterisk is the brand mark
- Don't put a heavy glass blur over the chat content — Anthropic's design is restrained, no glassmorphism on content
- Don't use pure black `#000000` for body text on cream — `#2D2520` warm ink is the correct value
- Don't render code blocks on blue-tinted dark surfaces — Claude's code surface is warm dark
- Don't animate streaming text character-by-character — word-by-word is the correct pace
- Don't add bottom tab bars — Claude is a single-purpose chat with sidebar navigation
- Don't blue-tint the dark mode — Claude's dark canvas (`#1F1B16`) is warm, slightly orange-undertoned
- Don't shrink the assistant body below 16pt — Claude is prose, not Slack
- Don't use cool greys anywhere — Claude's neutrals are all warm-tinted (cream, paper, sand, stone, ink)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | User pill max-width drops to 85%; code blocks shift to 14pt → 13pt body text for density |
| iPhone 13/14/15 | 390pt | Standard layout — 20pt margins, full-width chat |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; the 52pt header sits below status bar |
| iPhone 15/16 Pro Max | 430pt | Slightly larger code block padding (20pt vs 16pt); model chip gets extra breathing room |
| iPad | 768pt+ | Persistent 260pt sidebar; chat content max-width 720pt centered; artifact pane opens at 40% width on the right |
| iPad Pro 12.9 | 1024pt+ | Same 720pt max-width on chat — readability does not benefit from wider columns; artifact pane can grow to 50% |

### Dynamic Type
- Assistant body, all headings, user message text, list items, blockquotes: full scale
- Model chip, timestamps, "Press ⏎ to send" hint: fixed (layout-sensitive)
- Code block font: scales with reduced upper bound (max 18pt) to prevent overflow

### Orientation
- All chat surfaces: portrait + landscape supported
- On landscape iPhone: sidebar opens as a panel covering 60% of the width, not full-screen
- On iPad: persistent sidebar in both orientations; artifact pane re-flows

### Touch Targets
- Send circle: 40pt button on a 56pt hit area
- Model chip: 32pt tall, 44pt hit area
- Sidebar conversation rows: full-row 64pt tap target
- Copy button on code blocks: 32pt with a 44pt hit area

### Safe Area Handling
- Top: safe area honored — the 52pt header sits below status bar / Dynamic Island
- Bottom: input bar pins above home indicator with full safe area inset
- Sides: 20pt margins on iPhone; 32pt on iPad

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas (Cream Paper): `#F8F4ED`
- Paper White (input fill): `#FBF9F4`
- Surface Warm 1 (user pill): `#F0EAE0`
- Surface Warm 2 (chip fills): `#E8E0D2`
- Divider Sand: `#DDD2BD`
- Ink (primary text): `#2D2520`
- Graphite Warm (secondary text): `#5A4F44`
- Stone Warm (tertiary): `#8A7E72`
- Bone Warm (disabled): `#B5AB9E`
- Claude Orange (signature): `#D97757`
- Orange Pressed: `#BE6242`
- Orange Soft (active chip): `#F2DDD0`
- Code Background: `#1F1B16`
- Code keyword: `#D97757` (Claude Orange)
- Code string: `#7FB069` (sage)
- Code number: `#E8B96F` (gold)
- Code function: `#9DA4F2` (periwinkle)
- Dark canvas: `#1F1B16`
- Error: `#C16654`
- Success: `#6B9D5E`

### Example Component Prompts
- "Create a SwiftUI Claude assistant message block: a full-width flow with a 16pt asterisk-star logomark in `#D97757` Claude Orange at the leading edge, followed by a 'Claude · Claude Opus 4' sender label in Styrene 13pt Medium `#5A4F44`. Below the header strip, the body content renders as Tiempos Text 16pt Regular `#2D2520` with 1.55 line-height. Inside the body, full markdown rendering: H1 Tiempos Headline 24pt Semibold, H2 20pt, H3 17pt, all in `#2D2520`. Lists get 8pt gap between items. Inline code `code` uses JetBrains Mono 14pt Medium on `#F0EAE0` (Surface Warm 1) background with 4pt corner radius. A streaming orange cursor (8pt × 18pt `#D97757`) appears at the trailing edge while the model is generating, blinking 600ms on/off. No container background, no chat bubble — just type flowing across the cream canvas."
- "Build the Claude chat input bar: pinned above the keyboard, full-width minus 20pt margins. `#FBF9F4` Paper White background, 1pt `#DDD2BD` border default and 1.5pt `#D97757` border on focus. 20pt corner radius — capsule when single-line, softens slightly when grown. Min height 52pt, auto-grows to 200pt then becomes scrollable. Placeholder 'Reply to Claude…' in Styrene A 16pt Regular `#8A7E72`. Trailing the input: a 28pt paperclip-plus button leading a 40pt send circle. Send circle background `#D97757` Claude Orange when input has text, `#E8E0D2` Surface Warm 2 when empty; the 16pt arrow-up glyph inside is `#FBF9F4` when active, `#8A7E72` when disabled. Press the send circle: scale 0.94 → 1.0, `.impactOccurred(.medium)` haptic, then the user message pill slides up from the input area."
- "Design the Claude code block: 12pt corner radius, `#1F1B16` warm dark canvas background. A header strip at the top with 1pt `#3A332C` bottom divider — language name 'swift' in JetBrains Mono 11pt `#8A7E72` left-aligned, and a 32pt copy button right-aligned with `doc.on.doc` 14pt glyph in `#E8E0D2`. Body content: JetBrains Mono 14pt Regular `#E8E0D2` default text, with syntax highlighting in the Claude warm palette — keywords (`func`, `let`, `var`, `return`) in `#D97757` Claude Orange, strings in `#7FB069` sage, numbers in `#E8B96F` warm gold, function names in `#9DA4F2` soft periwinkle, comments in `#8A7E72`. 16pt padding all around. Horizontal scroll for long lines, no wrap. On copy: the icon morphs to `checkmark` over 150ms and a 'Copied' toast slides up from the bottom, dismissing after 1200ms."
- "Create the Claude model picker sheet: bottom sheet with 24pt top corner radius, `#FBF9F4` Paper White background. List of model rows — each 64pt tall, full width, with 20pt horizontal padding. Each row leads with a 24pt asterisk-star glyph in `#D97757` (Claude Orange) for active/available, or `#B5AB9E` (Bone Warm) for unavailable. Center column: model name in Styrene A 16pt Medium `#2D2520` ('Claude Opus 4.5'), sub-line in Styrene A 13pt Regular `#5A4F44` ('Most intelligent'). Trailing: a 12pt `#D97757` Claude Orange filled circle if this row is the currently selected model. 1pt `#F0EAE0` divider between rows. Tap selects and dismisses the sheet with a `.selectionChanged()` haptic. Background dim 0.4 opacity on the chat content beneath."
- "Build the Claude streaming response cursor: a small caret that appears at the trailing edge of Claude's response while the model is generating. Dimensions 8pt × 18pt, `#D97757` Claude Orange fill, 1pt corner radius. Animation: blinks at 600ms interval — 300ms visible, 300ms hidden. When a new token arrives the cursor pauses (stays visible) and only resumes blinking after a 200ms idle. Disappears when the response is complete and gets replaced by the action button row ('Continue', 'Regenerate', 'Copy'). The cursor sits inline with the text — descenders of Tiempos Text align with the bottom of the cursor."

### Iteration Guide
1. Canvas is `#F8F4ED` Cream Paper — the most recognizable Claude surface
2. Claude Orange `#D97757` is sacred — Send button, active model chip, asterisk-star avatar, streaming cursor, links only
3. Assistant body in Tiempos serif at 16pt with 1.55 line-height; user messages in Styrene/Inter sans — the typographic asymmetry is the brand
4. Ink `#2D2520` is warm near-black for primary text, never `#000000`
5. Code blocks live on warm dark `#1F1B16`, never blue — syntax uses orange/sage/gold/periwinkle
6. Asterisk-star logomark in `#D97757` — never any other color, never replaced with a sparkle or a "C"
7. No chat bubbles with tails — user pill (soft) + assistant flat flow
8. Streaming is word-by-word; cursor blinks 600ms; "Thinking…" pulses on extended-thinking models
9. Sidebar for conversation history (260pt iPad / full-screen iPhone); no bottom tab bar
10. Dark mode is warm `#1F1B16` canvas with `#E8E0D2` cream text; Claude Orange unchanged
