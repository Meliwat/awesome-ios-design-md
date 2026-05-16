# Design System Inspiration of Google Gemini (iOS)

## 1. Visual Theme & Atmosphere

Google Gemini's iOS app is a calm, conversational surface where the only chrome is the conversation itself. The canvas is clean white (`#FFFFFF`) in light mode and a deep neutral charcoal (`#1E1E1E`) in dark mode — there are no cards, no boxes, no list furniture. A conversation reads like a document: the user's turn sits in a soft gray chip aligned to the trailing edge, and the assistant's reply is plain flowing text on the canvas, marked only by a small gradient sparkle. This document-not-feed posture is the whole point: Gemini wants the model's words to feel like thinking made visible, not UI.

The accent system is a single, unmistakable gesture: the Gemini gradient — a smooth sweep from Google Blue (`#4285F4`) through violet (`#9B72CB`) to coral (`#D96570`). It is rationed to brand moments: the sparkle ✦ logomark, the streaming shimmer that runs across text as it generates, the focused state of the prompt bar, and the suggestion-chip accents. Solid Google Blue (`#4285F4`) is the interactive primary (send button active, links). Everything else is ink, gray, or the soft surface (`#F0F4F9` light / `#282A2C` dark). The gradient is precious — it appears where intelligence is happening.

Typography is Google Sans (Google's product typeface), rendered here with Inter as the closest free substitute, at weights 400 / 500 / 700. The hierarchy is restrained and reading-optimized: 12–28pt, weights concentrated at 400 (body/answers) and 500 (titles/labels). There are no oversized hero headlines — the answer text *is* the content, set at a comfortable 16pt with generous 1.55 line-height so long responses read like prose. The most expressive type moment is the streaming answer: text appears token-by-token with a soft gradient shimmer sweeping the leading edge as it writes.

**Key Characteristics:**
- Document-not-feed canvas — white (`#FFFFFF`) light / charcoal (`#1E1E1E`) dark, no cards
- The Gemini gradient (`#4285F4 → #9B72CB → #D96570`) as the only accent, rationed to brand moments
- Sparkle ✦ logomark — the gradient mark for "intelligence here"
- User turn = soft gray chip (trailing-aligned); assistant turn = plain text on canvas
- Streaming gradient shimmer — text generates token-by-token with a gradient edge sweep
- Prompt bar with mic + image add — the persistent, focused composer
- Suggestion chips — soft pills with gradient hairline accents below the greeting
- Side drawer navigation (no bottom tabs) — recent conversations + Gems
- Google Sans typeface (Inter fallback), weights 400/500/700, reading-optimized
- Full light + dark parity — the gradient is identical across themes

## 2. Color Palette & Roles

### Primary
- **Gemini Gradient**: Linear sweep `#4285F4` (0%) → `#9B72CB` (50%) → `#D96570` (100%). Used for the sparkle, streaming shimmer, focused prompt-bar ring, and chip accents — never as a flat fill behind body text.
- **Google Blue** (`#4285F4`): Interactive primary — active send button, links, selected states.
- **Blue Pressed** (`#3367D6`): Pressed state for the blue send button.
- **Gradient Violet** (`#9B72CB`): Mid-stop; used solo for subtle secondary accents.
- **Gradient Coral** (`#D96570`): End-stop; used solo for warm highlights (rare).

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary canvas — the conversation document.
- **Surface Soft** (`#F0F4F9`): User-turn chip, prompt-bar fill, suggestion chips, drawer.
- **Surface Raised** (`#FFFFFF` + hairline): Sheets, menus.
- **Divider** (`#E3E3E3`): Hairlines, chip borders, drawer separators.
- **Text Primary** (`#1F1F1F`): Answer text, titles, primary UI text.
- **Text Secondary** (`#5F6368`): Timestamps, helper text, placeholders, captions.
- **Text Tertiary** (`#9AA0A6`): Disabled labels, very low-emphasis meta.

### Canvas & Surfaces (Dark)
- **Dark Canvas** (`#1E1E1E`): Primary dark canvas — the conversation document.
- **Dark Surface** (`#282A2C`): User-turn chip, prompt-bar fill, suggestion chips, drawer.
- **Dark Divider** (`#3C3C3C`): Hairlines, chip borders, separators.
- **Dark Text Primary** (`#E3E3E3`): Answer text and titles on dark.
- **Dark Text Secondary** (`#9AA0A6`): Helper text and captions on dark.
- **Dark Blue** (`#8AB4F8`): Google Blue lightened for AA contrast on dark (links, send).

### Semantic
- **Success Green** (`#1E8E3E` light / `#81C995` dark): "Copied", confirmation toast.
- **Warning Amber** (`#F9AB00`): Rate-limit / safety notice banner.
- **Error Red** (`#D93025` light / `#F28B82` dark): Generation failed, network error.

## 3. Typography Rules

### Font Family
- **Primary**: `Google Sans` (Google product typeface) + `Google Sans Text` for body
- **Substitute / Fallback**: `Inter` — closest free geometric-humanist match
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Code blocks**: `Roboto Mono` / `'SF Mono', Menlo, monospace` for fenced code in answers

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Greeting (Hero) | Google Sans | 28pt | 500 | 1.2 | -0.2pt | "Hello, Alex" empty-conversation greeting |
| Screen Title | Google Sans | 22pt | 500 | 1.25 | -0.1pt | Drawer header, settings title |
| Section Header | Google Sans | 18pt | 500 | 1.3 | 0pt | "Recent", "Gems" in the drawer |
| Answer Heading | Google Sans | 20pt | 500 | 1.3 | 0pt | Markdown H2 inside a response |
| Answer Subheading | Google Sans | 17pt | 500 | 1.35 | 0pt | Markdown H3 inside a response |
| Answer Body | Google Sans Text | 16pt | 400 | 1.55 | 0pt | The model's prose — the core content |
| User Turn | Google Sans Text | 16pt | 400 | 1.45 | 0pt | Text inside the gray user chip |
| List Item (answer) | Google Sans Text | 16pt | 400 | 1.5 | 0pt | Bulleted/numbered list lines in a response |
| Prompt Input | Google Sans Text | 16pt | 400 | 1.4 | 0pt | Text typed into the prompt bar |
| Suggestion Chip | Google Sans Text | 14pt | 500 | 1.3 | 0pt | Prompt-suggestion pill label |
| Metadata | Google Sans Text | 13pt | 400 | 1.3 | 0pt | Timestamps, model label, drawer subtitle |
| Code | Roboto Mono | 14pt | 400 | 1.5 | 0pt | Fenced code blocks inside answers |
| Label (UPPER) | Google Sans | 11pt | 500 | 1.2 | 0.6pt | "TODAY" / "PREVIOUS 7 DAYS" drawer labels |
| Button | Google Sans | 15pt | 500 | 1.0 | 0pt | Text buttons, dialog actions |

### Principles
- **Weights at 400 / 500 / 700**: Regular for all reading text, medium for titles/labels/chips, bold reserved for strong emphasis inside answers — no thin
- **Reading-optimized body**: answer text is 16pt at 1.55 line-height so long responses read like prose, not chat snippets
- **The gradient never tints body text**: text is always solid ink; the gradient lives in the sparkle, the streaming edge, and chip accents
- **Negative tracking only on the greeting/titles**: -0.2pt at 28pt; body sits at 0pt
- **Dynamic Type respected**: greeting, answers, body scale; the prompt-bar height and drawer labels are layout-pinned

## 4. Component Stylings

### Buttons

**Send Button (prompt bar)**
- Shape: Circle, 36pt diameter, sits at the trailing edge of the prompt bar
- Empty state: glyph `arrow.up` 18pt, `#9AA0A6`, transparent background
- Active state (text present): background solid `#4285F4`, glyph `#FFFFFF`
- Pressed: scale 0.92, background `#3367D6`, soft haptic
- Generating: morphs to a `stop.fill` square in `#4285F4` to interrupt the stream

**Suggestion Chip**
- Background: `#F0F4F9` (light) / `#282A2C` (dark)
- Border: 1pt `#E3E3E3` (light) / `#3C3C3C` (dark) — OR a 1pt gradient hairline on featured chips
- Text: `#1F1F1F` / `#E3E3E3`, 14pt w500
- Padding: 10pt vertical, 16pt horizontal, corner radius 18pt
- Pressed: background darkens ~6%, scale 0.98
- Optional leading sparkle (12pt gradient) on AI-action chips

**Icon Action (Mic, Add image, Copy, Regenerate, Share, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#5F6368` (light) / `#9AA0A6` (dark)
- Active/primary: `#4285F4` / `#8AB4F8`
- Mic in the prompt bar: 22pt `#5F6368`; while listening it gets a gradient pulse ring

**Text Button (dialog "Cancel" / "Done")**
- Font: Google Sans, 15pt, weight 500
- Color: `#4285F4` / `#8AB4F8`
- No background, no underline, 44pt hit area

**Pill Button (primary dialog action)**
- Background: `#4285F4`
- Text: `#FFFFFF`, 15pt w500
- Padding: 10pt vertical, 24pt horizontal, corner radius 500pt
- Pressed: `#3367D6`, scale 0.98

### Cards & Containers

**User Turn Chip (The Signature)**
- Background: `#F0F4F9` (light) / `#282A2C` (dark)
- Text: `#1F1F1F` / `#E3E3E3`, 16pt w400
- Corner radius: 20pt (4pt on the bottom-trailing corner to "point" at the sender)
- Padding: 12pt vertical, 16pt horizontal
- Max width: ~80% of the conversation column, aligned to the trailing edge
- No avatar, no name — minimalism is the point

**Assistant Turn (The Signature)**
- No bubble, no background — plain markdown text directly on the canvas
- Leading: a small gradient sparkle ✦ (20pt) marking the start of the response
- Full conversation-column width, 16pt body at 1.55 line-height
- Footer action row appears after the stream completes: Copy, Regenerate, Share, ⋯ (22pt, `#5F6368`)
- Streaming: tokens append left→right with a soft gradient shimmer at the leading edge

**Code Block (inside an answer)**
- Background: `#F0F4F9` (light) / `#282A2C` (dark), corner radius 12pt
- Header bar: language label (13pt `#5F6368`) + a "Copy" text button
- Body: Roboto Mono 14pt, 1.5 line-height, 16pt padding, horizontal scroll
- Subtle 1pt `#E3E3E3` / `#3C3C3C` border

**Drawer Conversation Row**
- Height: 48pt
- Leading: a 18pt chat/sparkle glyph; title (16pt w400, 1-line truncating)
- Pressed: background `#F0F4F9` / `#282A2C`, corner radius 24pt (full-height pill highlight)
- Active conversation: persistent `#F0F4F9` / `#282A2C` pill + `#1F1F1F` text

**Sheet / Menu**
- Background: `#FFFFFF` / `#1E1E1E`, corner radius 20pt top corners, 16pt grabber
- Rows: 52pt, 1pt `#E3E3E3` / `#3C3C3C` dividers, leading 22pt glyph + label

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: hamburger / drawer toggle (24pt `#5F6368`)
- Center: model selector — "Gemini" with a small chevron, tappable to switch model (e.g., Flash / Pro), 17pt w500
- Trailing: new-chat / compose icon (22pt) + account avatar (28pt circular)
- Background: matches canvas; subtle hairline on scroll only

**Side Drawer**
- Width: ~82% of screen, slides from the leading edge over a `rgba(0,0,0,0.4)` scrim
- Top: a "New chat" pill (gradient-hairline or solid `#F0F4F9`) + search field
- Body: grouped recents under uppercase day labels ("TODAY", "PREVIOUS 7 DAYS"), then "Gems"
- Bottom: settings + account row
- No bottom tab bar — the drawer is the sole primary navigation

### Input Fields

**Prompt Bar (The Composer)**
- Background: `#F0F4F9` (light) / `#282A2C` (dark)
- Corner radius: 26pt, min height 52pt, full-width with 16pt side margins
- Leading: "+" add-content icon (22pt `#5F6368`) opening image/file/camera
- Center: text field, placeholder "Ask Gemini", 16pt w400 `#5F6368`
- Trailing: mic icon (22pt) and the send button (see Buttons)
- Focused: a 1.5pt **gradient ring** wraps the bar (the focus signal)
- Multiline: grows up to ~5 lines, then scrolls internally

**Search Field (drawer)**
- Background: `#FFFFFF` / `#1E1E1E` with a 1pt `#E3E3E3` / `#3C3C3C` border
- Height: 44pt, corner radius 22pt
- Leading `magnifyingglass` 18pt `#5F6368`, placeholder "Search chats"

### Distinctive Components

**Gradient Sparkle Mark**
- The ✦ four-point sparkle filled with the Gemini gradient (`#4285F4 → #9B72CB → #D96570`)
- Marks the assistant turn, the empty-state hero, and the app icon — the single brand signifier of "intelligence here"
- Sizes: 20pt (turn marker), 40–56pt (empty-state hero), 12pt (chip accent)

**Streaming Answer Shimmer**
- As a response generates, text appears token-by-token; a soft gradient highlight (~120pt wide, the Gemini gradient at low opacity) sweeps the leading edge of the newest text
- A 3-dot gradient "thinking" indicator shows before the first token arrives
- The send button morphs to a stop control while streaming

**Suggestion Chip Row**
- Below the empty-state greeting: 3–4 horizontally scrolling chips ("Brainstorm ideas", "Summarize a document", "Help me write")
- Featured/AI chips carry a 1pt gradient hairline border and a leading 12pt sparkle
- Tapping a chip seeds the prompt bar and focuses it

**Model Selector**
- A tappable "Gemini ⌄" in the top bar opens a sheet listing models (e.g., "2.5 Flash — fast", "2.5 Pro — most capable") with a gradient-sparkle leading each, current model checked

**Multimodal Attach Tray**
- The "+" opens a row of soft tiles: Camera, Photos, Files — each a 56pt rounded-square with a centered glyph; attached items appear as 64pt thumbnails above the prompt text with a remove ✕

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Conversation column: full width with 16pt side margins (single readable column)
- Turn spacing: 24pt between a user turn and the assistant reply; 32pt between exchanges
- Prompt bar: 16pt side margins, 12pt above the safe-area bottom

### Grid & Container
- Single-column conversation — no multi-column, no cards; reading flow is the grid
- User chip max width ~80%; assistant text spans the full column
- Suggestion chips: horizontal scroll, 8pt gaps, 16pt leading inset
- Drawer: ~82% width overlay

### Whitespace Philosophy
- **Document-grade reading space**: 1.55 line-height on answers and 24–32pt between turns make long responses calm to read — Gemini is a reading surface first
- **Minimal chrome**: no boxes around the assistant turn; whitespace and the sparkle do the structuring
- **The prompt bar is sacred**: always reachable, generously padded, never cramped

### Border Radius Scale
- Standard (12pt): Code blocks, small surfaces
- Chip (18pt): Suggestion chips
- Turn (20pt): User-turn chip (4pt on the pointing corner)
- Sheet (20pt): Bottom sheets / menus (top corners)
- Composer (26pt): The prompt bar
- Pill (500pt): "New chat" pill, dialog primary, drawer row highlight
- Circle (50%): Avatar, send button, mic, attach tiles

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Conversation canvas, assistant turn, user chip |
| Prompt Bar (Level 1) | `rgba(31,31,31,0.06) 0 1px 4px` (light) / subtle on dark | The composer floating above the canvas |
| Drawer (Level 2) | `rgba(31,31,31,0.16) 0 8px 32px` | Side drawer over the scrim |
| Sheet (Level 3) | `rgba(31,31,31,0.18) 0 -12px 32px` | Model selector, menus, attach tray |
| Focus Ring | 1.5pt **gradient** stroke (no blur) | The focused prompt bar — the gradient *is* the elevation cue |
| Scrim | `rgba(0,0,0,0.40)` | Behind the drawer and modal sheets |

**Shadow Philosophy**: Gemini is almost shadowless — a reading document does not stack. The only real elevation is the prompt bar (a whisper of shadow so it floats over scrolling text) and overlay surfaces (drawer/sheets). The single most important "elevation" device is non-shadow: the **gradient focus ring** on the prompt bar. Where most apps use a blue glow, Gemini uses its brand gradient to say "the model is listening." Everything in the conversation itself stays perfectly flat.

### Motion
- **Streaming tokens**: text appends at ~natural reading cadence; a gradient highlight sweeps the leading edge continuously while generating
- **Thinking indicator**: 3 dots cycling a gradient pulse before the first token
- **Send → stream**: send button morphs (circle → stop square) with a 200ms spring; on completion it morphs back
- **Prompt focus**: the gradient ring fades/scales in over 180ms when the field gains focus
- **Drawer**: slides in from the leading edge, 0.28s ease-out, scrim fades in parallel
- **Suggestion chip tap**: scale 0.98 + the chip's text flows into the prompt bar (cross-fade)
- **Sparkle idle**: a subtle, slow gradient-position shimmer on the empty-state hero sparkle (respects Reduce Motion)
- **Copy confirm**: a brief gradient-edged toast "Copied", 1.5s

## 7. Do's and Don'ts

### Do
- Treat the conversation as a document — white/charcoal canvas, no cards around turns
- Reserve the Gemini gradient for brand moments: the sparkle, streaming edge, focus ring, chip accents
- Render the user turn as a soft gray chip (trailing-aligned, 4pt pointing corner); the assistant turn as plain text with a leading gradient sparkle
- Use solid Google Blue (`#4285F4`) for the interactive send button and links — the gradient is not a button fill
- Stream answers token-by-token with a gradient shimmer at the leading edge
- Use the gradient focus ring on the prompt bar — it is the signature "listening" cue
- Keep answer body at 16pt with 1.55 line-height so long responses read like prose
- Provide full light + dark parity; the gradient is identical across themes
- Use Google Sans (Inter fallback) at weights 400/500/700, with Roboto Mono for code

### Don't
- Don't bubble the assistant turn — it is plain text on the canvas, not a card
- Don't tint body text with the gradient — text is always solid ink; the gradient is structural
- Don't use the gradient as a flat fill behind paragraphs or as a button background
- Don't add a bottom tab bar — the side drawer is the sole primary navigation
- Don't crowd the prompt bar or remove its generous padding — it is always reachable
- Don't use thin font weights — the ramp is 400/500/700
- Don't over-shadow — the conversation is flat; only the prompt bar and overlays elevate
- Don't skip the streaming shimmer / thinking indicator — generation must feel alive
- Don't diverge the gradient stops between light and dark — keep `#4285F4 → #9B72CB → #D96570` exact

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Conversation column tight to 16pt margins; chips scroll sooner |
| iPhone 13/14/15 | 390pt | Standard single-column conversation |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the top bar |
| iPhone 15/16 Pro Max | 430pt | Wider reading column, larger empty-state sparkle |
| iPad | 768pt+ | Persistent drawer (not overlay) + a centered ~720pt reading column |

### Dynamic Type
- Greeting, answer body/headings, user turn, list items: full scale
- Prompt-bar height: grows with text size but base height is layout-pinned
- Drawer day labels and metadata: fixed sizes
- Code blocks: scale but preserve monospace alignment

### Orientation
- Conversation: **portrait-primary**; landscape supported (column re-centers, max readable width)
- iPad: **all orientations**, persistent drawer + centered column
- Image/attachment viewer: **rotates** to fit media

### Touch Targets
- Send button: 36pt visual, 44pt hit area
- Mic / add / answer actions: 22pt glyph, 44pt hit area
- Suggestion chips: full chip tappable, ≥ 40pt tall
- Drawer rows: 48pt height, full-row tappable
- Model selector: full "Gemini ⌄" label tappable, 44pt tall

### Safe Area Handling
- Top: top bar respects safe area + Dynamic Island
- Bottom: prompt bar floats above the home indicator with 12pt clearance; KB-aware
- Drawer: full-height overlay respecting safe area, scrim covers the rest
- Conversation: respects safe area top/bottom; the prompt bar never overlaps the last turn (auto-scroll)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light/dark): `#FFFFFF` / `#1E1E1E`
- Surface soft (light/dark): `#F0F4F9` / `#282A2C`
- Divider (light/dark): `#E3E3E3` / `#3C3C3C`
- Text primary (light/dark): `#1F1F1F` / `#E3E3E3`
- Text secondary: `#5F6368` / `#9AA0A6`
- Gemini gradient: `#4285F4` → `#9B72CB` → `#D96570`
- Google Blue (interactive): `#4285F4` (dark: `#8AB4F8`)
- Blue pressed: `#3367D6`
- Error: `#D93025` / `#F28B82`

### Example Component Prompts
- "Create a SwiftUI Gemini conversation: a trailing-aligned user chip with `#F0F4F9` background, `#1F1F1F` text in Inter 16pt weight 400, 20pt corner radius with a 4pt bottom-trailing corner, max width 80%. Below it the assistant turn: NO bubble — plain markdown text on the white canvas, full-width, 16pt at 1.55 line-height, led by a 20pt sparkle filled with a linear gradient `#4285F4 → #9B72CB → #D96570`."
- "Build the prompt bar: full-width `#F0F4F9` field, 26pt corner radius, min height 52pt, 16pt side margins. Leading '+' add icon (22pt `#5F6368`), placeholder 'Ask Gemini' 16pt `#5F6368`, trailing mic (22pt) and a 36pt send button. Send is `#9AA0A6` arrow when empty and a solid `#4285F4` circle with a white arrow when text is present. On focus, wrap the whole bar in a 1.5pt gradient ring (`#4285F4 → #9B72CB → #D96570`)."
- "Design the streaming answer: assistant text appends token-by-token; overlay a ~120pt-wide soft gradient highlight that sweeps the leading edge while generating. Before the first token, show a 3-dot gradient pulse 'thinking' indicator. The send button morphs to a `#4285F4` stop square during streaming."
- "Create the suggestion chip row under a 28pt weight 500 greeting 'Hello, Alex': 3 horizontally scrolling chips, `#F0F4F9` fill, 18pt corner radius, 14pt weight 500 label, featured chips get a 1pt gradient hairline border and a 12pt leading sparkle. Tapping a chip seeds and focuses the prompt bar."
- "Build the side drawer: ~82% width, slides from the leading edge over a `rgba(0,0,0,0.40)` scrim. Top: a 'New chat' pill + a search field (44pt, 1pt `#E3E3E3` border). Body: recents grouped under uppercase labels 'TODAY' / 'PREVIOUS 7 DAYS', each row 48pt with a leading 18pt glyph; the active row gets a `#F0F4F9` full-height pill highlight. No bottom tab bar."

### Iteration Guide
1. The conversation is a document — white/charcoal canvas, no cards; user = gray chip, assistant = plain text
2. The Gemini gradient (`#4285F4 → #9B72CB → #D96570`) is rationed to the sparkle, streaming edge, focus ring, and chip accents — never a body-text tint or button fill
3. Solid Google Blue `#4285F4` is the interactive primary (send active, links)
4. Stream answers token-by-token with a gradient leading-edge shimmer; show a thinking indicator first
5. The prompt bar's gradient focus ring is the signature "listening" cue
6. Body answers are 16pt at 1.55 line-height — a reading surface, not chat snippets
7. Navigation is a side drawer only — no bottom tabs
8. Full light + dark parity; keep the gradient stops identical across themes; Inter fallback, 400/500/700
