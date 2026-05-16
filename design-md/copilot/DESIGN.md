# Design System Inspiration of Microsoft Copilot (iOS)

## 1. Visual Theme & Atmosphere

Microsoft Copilot's iOS app is a warm, approachable assistant wrapped in Microsoft's Fluent design language. The canvas is clean white (`#FFFFFF`) in light mode and a deep neutral (`#202020`) in dark mode, but the defining material is **Fluent acrylic**: frosted, lightly tinted, blurred surfaces that sit *over* content (the prompt bar, sheets, the suggestion tray) so the UI feels physical and layered rather than flat. Where Gemini is a bare document, Copilot is a soft, tactile workspace — answers arrive in gently rounded cards, surfaces have depth, and the whole thing reads friendly and consumer-warm, not enterprise-cold.

The accent system pairs the Copilot flourish gradient — a warm sweep from coral (`#FF6F61`) to gold (`#FFB900`) — with Microsoft's Fluent Blue (`#0078D4`) as the interactive primary. The flourish gradient is precious: it fills the swirl/flourish logomark, the streaming shimmer, and the focused prompt-bar accent — the brand's "thinking happening" signal. Fluent Blue owns the functional primary (active send, links, selected toggles, the tone selector's active state). Everything else is ink, gray, or an acrylic-tinted surface (`#F3F3F3` light / `#2D2D2D` dark). The warm flourish + cool blue duality is intentional: warmth for personality, blue for action.

Typography is Segoe UI (Microsoft's product typeface), rendered here with Inter as the closest free substitute, at weights 400 / 600 / 700. The hierarchy is friendly and reading-optimized: 12–28pt, with weights at 400 (body/answers), 600 (titles/labels/chips), and 700 (strong emphasis). There are no oversized headlines — the answer text *is* the content, set at 16pt with comfortable 1.5 line-height inside soft cards. The most expressive type moment is the streaming answer card, where text generates with a warm flourish shimmer and the card subtly settles into place with an acrylic blur-fade.

**Key Characteristics:**
- Warm Fluent workspace — white (`#FFFFFF`) light / neutral (`#202020`) dark, layered not flat
- Fluent acrylic surfaces — frosted, tinted, blurred prompt bar / sheets / suggestion tray
- Copilot flourish gradient (`#FF6F61 → #FFB900`) — the swirl mark, streaming shimmer, focus accent
- Fluent Blue (`#0078D4`) as the functional primary — active send, links, selected toggles
- Conversational answer cards — soft rounded cards on the canvas (not bare text)
- Tone / style toggle — segmented control (Creative / Balanced / Precise) shaping responses
- Prompt-suggestion chips — acrylic pills below the greeting and after answers
- Voice button — prominent mic with a flourish pulse while listening
- Top bar + sidebar nav (no bottom tabs) — recent chats + Copilot pages in a drawer
- Segoe UI typeface (Inter fallback), weights 400/600/700, full light + dark parity

## 2. Color Palette & Roles

### Primary
- **Copilot Flourish Gradient**: Linear sweep `#FF6F61` (0%) → `#FFB900` (100%). Used for the flourish mark, streaming shimmer, and the focused prompt accent — never a flat fill behind body text.
- **Fluent Blue** (`#0078D4`): Interactive primary — active send button, links, selected tone, toggles.
- **Blue Pressed** (`#005A9E`): Pressed state for the blue send / primary actions.
- **Blue Tint** (`#DEECF9`): Selected-chip wash, focused-field subtle ring (light).
- **Flourish Coral** (`#FF6F61`): Gradient start; used solo for warm secondary accents (rare).
- **Flourish Gold** (`#FFB900`): Gradient end; used solo for warm highlights / "new" dots.

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary canvas behind the conversation.
- **Surface Acrylic** (`#F3F3F3`): Answer cards, prompt-bar fill, suggestion chips, sidebar (rendered with a frosted blur over content).
- **Surface Raised** (`#FFFFFF` + soft shadow + 1pt hairline): Menus, dialogs.
- **Divider** (`#E0E0E0`): Hairlines, card borders, sidebar separators.
- **Text Primary** (`#242424`): Answer text, titles, primary UI text (warm near-black).
- **Text Secondary** (`#616161`): Timestamps, helper text, placeholders, captions.
- **Text Tertiary** (`#919191`): Disabled labels, very low-emphasis meta.

### Canvas & Surfaces (Dark)
- **Dark Canvas** (`#202020`): Primary dark canvas behind the conversation.
- **Dark Surface Acrylic** (`#2D2D2D`): Answer cards, prompt bar, chips, sidebar (frosted dark blur).
- **Dark Divider** (`#3A3A3A`): Hairlines, card borders, separators.
- **Dark Text Primary** (`#FFFFFF`): Answer text and titles on dark.
- **Dark Text Secondary** (`#A6A6A6`): Helper text and captions on dark.
- **Dark Blue** (`#4DA3E0`): Fluent Blue lightened for AA contrast on the dark canvas.

### Semantic
- **Success Green** (`#107C10` light / `#6CCB5F` dark): "Copied", confirmation toast.
- **Warning Amber** (`#F7630C`): Rate-limit / content notice banner.
- **Error Red** (`#C50F1F` light / `#FF99A4` dark): Generation failed, network error.

## 3. Typography Rules

### Font Family
- **Primary**: `Segoe UI` / `Segoe UI Variable` (Microsoft product typeface)
- **Substitute / Fallback**: `Inter` — closest free humanist-grotesque match
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Code blocks**: `Cascadia Code` / `'SF Mono', Menlo, monospace` for fenced code in answers

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Greeting (Hero) | Segoe UI | 28pt | 700 | 1.2 | -0.2pt | "Hi, I'm Copilot" empty-state greeting |
| Screen Title | Segoe UI | 22pt | 700 | 1.25 | -0.1pt | Sidebar header, settings title |
| Section Header | Segoe UI | 18pt | 600 | 1.3 | 0pt | "Recent", "Copilot Pages" in the sidebar |
| Answer Heading | Segoe UI | 20pt | 600 | 1.3 | 0pt | Markdown H2 inside a response card |
| Answer Subheading | Segoe UI | 17pt | 600 | 1.35 | 0pt | Markdown H3 inside a response card |
| Answer Body | Segoe UI | 16pt | 400 | 1.5 | 0pt | The model's prose — the core content |
| User Turn | Segoe UI | 16pt | 400 | 1.45 | 0pt | Text inside the user card/bubble |
| List Item (answer) | Segoe UI | 16pt | 400 | 1.5 | 0pt | Bulleted/numbered list lines in a response |
| Prompt Input | Segoe UI | 16pt | 400 | 1.4 | 0pt | Text typed into the prompt bar |
| Suggestion Chip | Segoe UI | 14pt | 600 | 1.3 | 0pt | Prompt-suggestion pill label |
| Tone Label | Segoe UI | 13pt | 600 | 1.0 | 0.1pt | "Creative / Balanced / Precise" segment |
| Metadata | Segoe UI | 13pt | 400 | 1.3 | 0pt | Timestamps, model label, sidebar subtitle |
| Code | Cascadia Code | 14pt | 400 | 1.5 | 0pt | Fenced code blocks inside answers |
| Label (UPPER) | Segoe UI | 11pt | 600 | 1.2 | 0.6pt | "TODAY" / "LAST 7 DAYS" sidebar labels |
| Button | Segoe UI | 15pt | 600 | 1.0 | 0pt | Text buttons, dialog actions |

### Principles
- **Weights at 400 / 600 / 700**: Regular for all reading text, semibold for titles/labels/chips, bold for the greeting and strong in-answer emphasis — no thin
- **Reading-optimized body**: answer text is 16pt at 1.5 line-height so long responses read comfortably inside cards
- **The flourish gradient never tints body text**: text is always solid ink; the gradient lives in the flourish mark, the streaming edge, and the focus accent
- **Negative tracking only on the greeting/titles**: -0.2pt at 28pt; body sits at 0pt
- **Dynamic Type respected**: greeting, answers, body scale; the prompt-bar height and sidebar labels are layout-pinned

## 4. Component Stylings

### Buttons

**Send Button (prompt bar)**
- Shape: Circle, 36pt diameter, trailing edge of the prompt bar
- Empty state: glyph `arrow.up` 18pt, `#616161`, transparent background
- Active state (text present): background solid `#0078D4`, glyph `#FFFFFF`
- Pressed: scale 0.92, background `#005A9E`, soft haptic
- Generating: morphs to a `stop.fill` square in `#0078D4` to interrupt the stream

**Voice Button**
- Shape: Circle, 44pt; sits beside the prompt bar or as a prominent empty-state CTA
- Idle: acrylic surface fill, mic glyph 20pt `#616161`
- Listening: a flourish-gradient pulse ring radiates; mic glyph turns `#0078D4`
- Pressed: scale 0.94, haptic

**Suggestion Chip**
- Background: acrylic `#F3F3F3` (light) / `#2D2D2D` (dark), 1pt `#E0E0E0` / `#3A3A3A` border
- Text: `#242424` / `#FFFFFF`, 14pt w600
- Padding: 10pt vertical, 16pt horizontal, corner radius 18pt
- Pressed: surface darkens ~6%, scale 0.98
- Optional leading 12pt flourish glyph on AI-action chips

**Icon Action (Mic, Add, Copy, Regenerate, Like/Dislike, Share, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#616161` (light) / `#A6A6A6` (dark)
- Active/primary: `#0078D4` / `#4DA3E0`
- Like/Dislike feedback: thumbs glyphs; selected thumb fills `#0078D4`

**Text Button (dialog "Cancel" / "Done")**
- Font: Segoe UI, 15pt, weight 600
- Color: `#0078D4` / `#4DA3E0`
- No background, no underline, 44pt hit area

**Pill Button (primary dialog action)**
- Background: `#0078D4`
- Text: `#FFFFFF`, 15pt w600
- Padding: 10pt vertical, 24pt horizontal, corner radius 8pt (Fluent uses softer-rect, not full-pill, for primaries)
- Pressed: `#005A9E`, scale 0.98

### Cards & Containers

**Answer Card (The Signature)**
- Background: acrylic `#F3F3F3` (light) / `#2D2D2D` (dark) — frosted blur over the canvas
- Corner radius: 16pt, soft shadow `rgba(36,36,36,0.06) 0 2px 10px`, optional 1pt hairline
- Padding: 16pt; leading a 22pt flourish glyph marking the start of the response
- Full conversation-column width; 16pt body at 1.5 line-height
- Footer action row after the stream: Copy, Like, Dislike, Regenerate, Share, ⋯
- Streaming: a warm flourish shimmer sweeps the leading edge; the card does an acrylic blur-fade as it first appears

**User Turn**
- Background: `#0078D4` at full (or a soft `#DEECF9` light / `#2D2D2D` dark "neutral" variant per setting)
- Text: white on blue / `#242424` on neutral, 16pt w400
- Corner radius: 16pt (4pt on the bottom-trailing corner), trailing-aligned, max ~80% width
- Padding: 12pt vertical, 16pt horizontal

**Tone Selector**
- A 3-segment pill control: "Creative" / "Balanced" / "Precise"
- Track: acrylic surface; selected segment fills `#0078D4` with white label, others `#616161`
- Sits above/below the prompt bar; changing it visibly re-themes the next answer's accent intensity
- Corner radius 500pt, 13pt w600 labels, 36pt tall

**Code Block (inside an answer card)**
- Background: a deeper acrylic `#EDEDED` (light) / `#1B1B1B` (dark), corner radius 12pt
- Header bar: language label (13pt `#616161`) + a "Copy" text button
- Body: Cascadia Code 14pt, 1.5 line-height, 16pt padding, horizontal scroll
- 1pt `#E0E0E0` / `#3A3A3A` border

**Sidebar Conversation Row**
- Height: 48pt
- Leading: an 18pt chat glyph; title (16pt w400, 1-line truncating)
- Pressed: acrylic `#F3F3F3` / `#2D2D2D` highlight, corner radius 8pt
- Active conversation: persistent acrylic highlight + a 3pt leading `#0078D4` accent bar

**Sheet / Menu**
- Background: acrylic over a scrim, corner radius 16pt top corners, 16pt grabber
- Rows: 52pt, 1pt `#E0E0E0` / `#3A3A3A` dividers, leading 22pt glyph + label

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: sidebar toggle (24pt `#616161`)
- Center: "Copilot" wordmark or model/persona selector with a small chevron, 17pt w600
- Trailing: new-chat icon (22pt) + account avatar (28pt circular)
- Background: acrylic, subtle hairline on scroll

**Sidebar (Drawer)**
- Width: ~80% of screen, slides from the leading edge over a `rgba(0,0,0,0.4)` scrim
- Top: a "New chat" button (acrylic or `#0078D4`) + search field
- Body: recents grouped under uppercase labels ("TODAY", "LAST 7 DAYS"), then "Copilot Pages"
- Bottom: settings + account row
- No bottom tab bar — the sidebar is the sole primary navigation

### Input Fields

**Prompt Bar (The Composer, acrylic)**
- Background: acrylic `#F3F3F3` (light) / `#2D2D2D` (dark) — frosted blur over the conversation
- Corner radius: 24pt, min height 52pt, full-width with 16pt side margins
- Leading: "+" add-content icon (22pt `#616161`) opening image/file/camera
- Center: text field, placeholder "Message Copilot", 16pt w400 `#616161`
- Trailing: voice mic (22pt) and the send button (see Buttons)
- Focused: a 1.5pt accent stroke (Fluent Blue, or a flourish-gradient variant on emphasis)
- Multiline: grows up to ~5 lines, then scrolls internally

**Search Field (sidebar)**
- Background: acrylic with a 1pt `#E0E0E0` / `#3A3A3A` border
- Height: 44pt, corner radius 8pt
- Leading `magnifyingglass` 18pt `#616161`, placeholder "Search chats"

### Distinctive Components

**Flourish Mark**
- The Copilot swirl/flourish, filled with the flourish gradient (`#FF6F61 → #FFB900`)
- Marks the answer card, the empty-state hero, and the app icon — the brand signifier of "Copilot is thinking/here"
- Sizes: 22pt (card marker), 44–56pt (empty-state hero), 12pt (chip accent)

**Fluent Acrylic Surface**
- The defining material: prompt bar, sheets, sidebar, and chips are frosted/blurred and lightly tinted so the UI is layered over content
- Acrylic dims (does not hide) what's behind it; on first appearance a surface does a quick blur-in (opacity + blur ramp)

**Streaming Answer Shimmer**
- As a response generates inside its card, text appears token-by-token; a soft flourish-gradient highlight (~120pt wide, low opacity) sweeps the leading edge
- A 3-dot flourish "thinking" indicator shows before the first token; the send button morphs to a stop control while streaming

**Tone / Style Toggle**
- The Creative / Balanced / Precise segmented control is a Copilot signature — it visibly shapes the response (and the accent intensity) and persists across the session

**Prompt-Suggestion Chips**
- Below the greeting and after each answer: 3–4 horizontally scrolling acrylic chips ("Draft an email", "Explain this", "Make an image")
- AI-action chips carry a 12pt leading flourish glyph; tapping seeds + focuses the prompt bar

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Conversation column: full width with 16pt side margins (single readable column of cards)
- Turn spacing: 16pt between a user turn and its answer card; 24pt between exchanges
- Prompt bar: 16pt side margins, 12pt above the safe-area bottom

### Grid & Container
- Single-column conversation of soft cards — no multi-column
- User turn max width ~80%; answer card spans the full column
- Suggestion chips: horizontal scroll, 8pt gaps, 16pt leading inset
- Sidebar: ~80% width overlay

### Whitespace Philosophy
- **Comfortable, layered reading space**: 1.5 line-height, 16pt card padding, and 16–24pt between turns make long responses calm — Copilot is a reading surface with depth
- **Acrylic creates hierarchy**: blurred, tinted surfaces (not heavy shadows) communicate "this floats above the conversation"
- **The prompt bar is sacred**: always reachable, generously padded, acrylic so it reads as a persistent layer

### Border Radius Scale
- Standard (8pt): Primary pill button, sidebar row highlight, search field
- Code (12pt): Code blocks
- Card (16pt): Answer card, user turn (4pt on the pointing corner), sheets (top corners)
- Chip (18pt): Suggestion chips
- Composer (24pt): The prompt bar
- Pill (500pt): Tone selector
- Circle (50%): Avatar, send button, voice button, flourish accents

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Conversation canvas background |
| Card (Level 1) | `rgba(36,36,36,0.06) 0 2px 10px` + acrylic tint | Answer cards on the canvas |
| Acrylic Layer (Level 2) | Frosted blur (~24 radius) + tint + 1pt hairline | Prompt bar, suggestion tray, chips |
| Sidebar (Level 3) | Acrylic + `rgba(36,36,36,0.16) 0 8px 32px` | Side drawer over the scrim |
| Sheet (Level 4) | Acrylic + `rgba(36,36,36,0.18) 0 -12px 32px` | Menus, model/persona selector |
| Focus Accent | 1.5pt Fluent Blue (or flourish-gradient) stroke, no blur | Focused prompt bar |
| Scrim | `rgba(0,0,0,0.40)` | Behind the sidebar and modal sheets |

**Shadow Philosophy**: Copilot's depth is **material, not just shadow**. Fluent acrylic — a frosted, tinted, blurred layer — is the primary elevation device: the prompt bar, sidebar, and chips read as physical sheets floating over the conversation because you can see content faintly through them. Cards add a whisper of soft shadow on top of their acrylic tint. The flourish gradient handles brand emphasis (mark, streaming edge); Fluent Blue handles the functional focus accent. The conversation canvas itself stays flat — depth lives in the layered surfaces.

### Motion
- **Acrylic blur-in**: surfaces (prompt bar, sheets, a new answer card) ramp opacity + blur over 220ms as they appear
- **Streaming tokens**: text appends at natural reading cadence; a flourish-gradient highlight sweeps the leading edge while generating
- **Thinking indicator**: 3 dots cycling a flourish pulse before the first token
- **Send → stream**: send button morphs (circle → stop square) with a 200ms spring; reverses on completion
- **Tone change**: the selector's pill slides 0.2s spring; the next answer's accent intensity visibly shifts
- **Prompt focus**: the accent stroke fades/scales in over 180ms on focus
- **Sidebar**: slides in from the leading edge, 0.28s ease-out, scrim + acrylic fade in parallel
- **Suggestion chip tap**: scale 0.98 + the chip text flows into the prompt bar (cross-fade)
- **Flourish idle**: a slow gradient-position shimmer on the empty-state hero flourish (respects Reduce Motion)
- **Copy confirm**: a brief acrylic toast "Copied", 1.5s

## 7. Do's and Don'ts

### Do
- Build surfaces as Fluent acrylic — frosted, tinted, blurred sheets that float over the conversation
- Render answers in soft rounded cards (16pt radius, acrylic tint, whisper shadow) — not bare text
- Reserve the flourish gradient (`#FF6F61 → #FFB900`) for the flourish mark, streaming edge, and focus emphasis
- Use solid Fluent Blue (`#0078D4`) for the functional primary — active send, links, selected tone/toggles
- Keep the Creative / Balanced / Precise tone selector visible — it is a Copilot signature
- Stream answers token-by-token with a warm flourish shimmer at the leading edge
- Do an acrylic blur-in (opacity + blur ramp, 220ms) when a surface or new card appears
- Keep answer body at 16pt with 1.5 line-height inside cards
- Provide full light + dark parity; keep the flourish stops identical across themes
- Use Segoe UI (Inter fallback) at weights 400/600/700, Cascadia Code for code

### Don't
- Don't render answers as bare unboxed text — Copilot uses soft acrylic cards
- Don't tint body text with the flourish gradient — text is always solid ink; the gradient is structural
- Don't use the flourish gradient as a button fill or a flat panel background
- Don't make surfaces flat/opaque where acrylic is expected — the frosted layering is the brand
- Don't add a bottom tab bar — the sidebar is the sole primary navigation
- Don't full-pill the primary action button — Fluent primaries use a softer 8pt rounded rect (the tone selector is the pill)
- Don't use thin font weights — the ramp is 400/600/700
- Don't skip the streaming shimmer / thinking indicator — generation must feel alive
- Don't diverge the flourish stops between light and dark — keep `#FF6F61 → #FFB900` exact

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cards tight to 16pt margins; chips scroll sooner |
| iPhone 13/14/15 | 390pt | Standard single-column card conversation |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the acrylic top bar |
| iPhone 15/16 Pro Max | 430pt | Wider reading column, larger empty-state flourish |
| iPad | 768pt+ | Persistent sidebar (not overlay) + a centered ~720pt card column |

### Dynamic Type
- Greeting, answer body/headings, user turn, chips: full scale
- Prompt-bar height: grows with text size but base height is layout-pinned
- Sidebar day labels and tone labels: fixed sizes
- Code blocks: scale but preserve monospace alignment

### Orientation
- Conversation: **portrait-primary**; landscape supported (column re-centers, max readable width)
- iPad: **all orientations**, persistent sidebar + centered card column
- Image/attachment viewer: **rotates** to fit media

### Touch Targets
- Send button: 36pt visual, 44pt hit area
- Voice button: 44pt
- Mic / add / card actions: 22pt glyph, 44pt hit area
- Suggestion chips: full chip tappable, ≥ 40pt tall
- Tone segments: each ≥ 44pt wide, 36pt tall
- Sidebar rows: 48pt height, full-row tappable

### Safe Area Handling
- Top: acrylic top bar respects safe area + Dynamic Island
- Bottom: acrylic prompt bar floats above the home indicator with 12pt clearance; KB-aware
- Sidebar: full-height overlay respecting safe area, scrim covers the rest
- Conversation: respects safe area; the prompt bar never overlaps the last card (auto-scroll)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light/dark): `#FFFFFF` / `#202020`
- Surface acrylic (light/dark): `#F3F3F3` / `#2D2D2D`
- Divider (light/dark): `#E0E0E0` / `#3A3A3A`
- Text primary (light/dark): `#242424` / `#FFFFFF`
- Text secondary: `#616161` / `#A6A6A6`
- Copilot flourish gradient: `#FF6F61` → `#FFB900`
- Fluent Blue (interactive): `#0078D4` (dark: `#4DA3E0`)
- Blue pressed: `#005A9E`
- Error: `#C50F1F` / `#FF99A4`

### Example Component Prompts
- "Create a SwiftUI Copilot answer card: an acrylic `#F3F3F3` surface (use `.regularMaterial` over the white canvas), 16pt corner radius, soft shadow `rgba(36,36,36,0.06) 0 2px 10px`, 16pt padding. Lead with a 22pt flourish glyph filled with a linear gradient `#FF6F61 → #FFB900`. Body text in Inter 16pt weight 400 `#242424` at 1.5 line-height. Footer row after streaming: Copy, Like, Dislike, Regenerate, Share, ⋯ (22pt `#616161`)."
- "Build the acrylic prompt bar: full-width frosted `#F3F3F3` field (`.regularMaterial` blur over content), 24pt corner radius, min height 52pt, 16pt side margins. Leading '+' icon (22pt `#616161`), placeholder 'Message Copilot' 16pt `#616161`, trailing mic (22pt) and a 36pt send button that is `#616161` when empty and a solid `#0078D4` circle with a white arrow when text is present. On focus, add a 1.5pt `#0078D4` accent stroke."
- "Design the tone selector: a 3-segment pill (Creative / Balanced / Precise) on an acrylic track, 500pt corner radius, 36pt tall, 13pt weight 600 labels. The selected segment fills `#0078D4` with white text; the others are `#616161`. The pill slides 0.2s spring on change."
- "Create the streaming answer: text appends token-by-token inside the card; overlay a ~120pt-wide low-opacity flourish-gradient (`#FF6F61 → #FFB900`) highlight that sweeps the leading edge while generating. Show a 3-dot flourish pulse 'thinking' indicator before the first token. The card does an acrylic blur-in (opacity + blur ramp, 220ms) on first appearance; the send button morphs to a `#0078D4` stop square mid-stream."
- "Build the sidebar: ~80% width acrylic drawer sliding from the leading edge over a `rgba(0,0,0,0.40)` scrim. Top: a 'New chat' button (8pt rounded rect) + a 44pt search field. Body: recents grouped under uppercase labels 'TODAY' / 'LAST 7 DAYS', each row 48pt; the active row gets an acrylic highlight + a 3pt leading `#0078D4` accent bar. No bottom tab bar."

### Iteration Guide
1. Surfaces are Fluent acrylic — frosted, tinted, blurred layers over the conversation, not flat opaque panels
2. Answers live in soft acrylic cards (16pt radius, whisper shadow) — never bare unboxed text
3. The flourish gradient (`#FF6F61 → #FFB900`) is rationed to the flourish mark, streaming edge, and focus emphasis — never a body-text tint or button fill
4. Solid Fluent Blue `#0078D4` is the functional primary (active send, links, selected tone/toggles)
5. The Creative / Balanced / Precise tone selector is a Copilot signature — keep it visible and consequential
6. Stream answers token-by-token with a warm flourish shimmer; show a thinking indicator first; blur-in surfaces (220ms)
7. Navigation is a sidebar only — no bottom tabs; Fluent primaries use an 8pt rounded rect, not a full pill
8. Full light + dark parity; keep the flourish stops identical; Inter fallback, weights 400/600/700, Cascadia Code for code
