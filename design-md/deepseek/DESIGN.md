# Design System Inspiration of DeepSeek (iOS)

## 1. Visual Theme & Atmosphere

DeepSeek's iOS app is a focused, near-monochrome chat interface organized around one idea: **show the model thinking before it answers**. The canvas is a deep near-black (`#0E0E10` in dark; a clean white `#FFFFFF` in light), and the only saturated color anywhere is **DeepSeek Blue** (`#4D6BFE`) — a vivid indigo-blue that appears on the whale logomark, the user's message bubble, the send button, and the active **DeepThink (R1)** / **Search** toggles. Everything else is a restrained grey ramp. The result feels like a research tool: serious, quiet, and engineered to make the *reasoning* the spectacle rather than the chrome.

The signature interaction — and the thing that must be reproduced faithfully — is the **reasoning trace**. When DeepThink (R1) is enabled, the assistant's reply is preceded by a collapsible **reasoning panel**: a recessed surface (`#16171A` in dark — deliberately *darker* than the canvas) with a 2pt DeepSeek-Blue left-bar, a header that reads "Thought for N seconds · tap to collapse", and the model's chain-of-thought rendered in **dimmed italic** (`#8A8B90`). Below the panel sits the final answer in full-contrast text (`#ECECEC`). The visual hierarchy is intentional: the reasoning is present but subordinate; the answer is primary. Tapping the header collapses the trace to a single line. This "thinking-then-answering" pattern is DeepSeek's entire identity.

The second pillar is the **two-toggle composer**. Above the input pill sit two pill toggles: **DeepThink (R1)** — switches from the fast V3 model to the R1 reasoning model (which produces the trace) — and **Search** — grounds the answer in live web results with inline citations. When active, a toggle fills with a soft blue tint (`#1E2240`), a blue border (`#4D6BFE`), and blue text/icon. These two toggles are the primary controls of the product; the input field itself is a quiet rounded pill.

Typography is the platform sans (Inter as the open fallback, SF Pro on device) — no custom display face. Message body is 15pt regular; the reasoning trace is 13pt **italic** (the italicization is a deliberate "this is internal monologue" signal); code is monospace on a `#232428` chip with a faintly blue-tinted token color (`#C5CDFF`). The type system is conventional on purpose: DeepSeek's personality lives in the reasoning trace and the single electric blue, not in the letterforms.

**Key Characteristics:**
- Near-black canvas (`#0E0E10`) dark / white (`#FFFFFF`) light — quiet, research-tool calm
- Single accent: DeepSeek Blue `#4D6BFE` — whale mark, user bubble, send button, active toggles only
- Reasoning trace — collapsible recessed panel (`#16171A`) with a blue left-bar, chain-of-thought in dimmed italic, subordinate to the answer
- "Thought for N seconds" header — tap to collapse/expand the trace
- DeepThink (R1) toggle — switches V3 (fast) → R1 (reasoning, produces the trace)
- Search toggle — grounds answers in web results with inline citations
- User bubble: soft-blue fill (`#1E2240`) with asymmetric corner (18/18/4/18); AI replies: no bubble, full-width
- Whale logomark — the brand glyph, always in DeepSeek Blue
- Monospace code chips with a blue-tinted token color (`#C5CDFF`)
- Minimal chrome: bottom tabs (Chat / History / Settings), thin top bar with the whale wordmark

## 2. Color Palette & Roles

### Primary (Interactive)
- **DeepSeek Blue** (`#4D6BFE`): The single accent — whale logomark, send button, active toggle border/text/icon, links, primary CTA fill.
- **DeepSeek Blue Pressed** (`#3B57E0`): Pressed state of blue-filled buttons.
- **DeepSeek Blue Soft** (`#1E2240` dark / `#EBEFFF` light): User-bubble fill, active-toggle fill, soft-button background.
- **Blue Token** (`#C5CDFF` dark / `#3B57E0` light): Inline-code token color (a blue-tinted mono).

### Canvas & Surfaces (Dark)
- **Canvas** (`#0E0E10`): Primary dark canvas — near-black, faintly cool.
- **Surface 1** (`#1A1B1E`): Composer field, toggle resting background, cards, sections.
- **Surface 2** (`#232428`): Code chips, raised rows, hovered surfaces.
- **Surface 3** (`#2C2D31`): Pressed surface, menu rows.
- **Reasoning BG** (`#16171A`): The reasoning-trace panel — intentionally *darker* than the canvas to recede.
- **Divider** (`#2E2F33`): Hairline dividers, input/toggle borders.

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface 1 Light** (`#F5F6F8`): Composer field, cards.
- **Surface 2 Light** (`#ECEEF2`): Code chips, raised rows.
- **Reasoning BG Light** (`#F7F8FA`): Reasoning-trace panel (slightly off-white to recede).
- **Divider Light** (`#E4E6EB`): Hairlines, borders.

### Text
- **Text Primary** (`#ECECEC` dark / `#1A1B1E` light): Final answer, message body, titles.
- **Text Secondary** (`#9A9BA0` dark / `#6A6B70` light): Metadata ("Thought for N seconds"), model name, sub-labels.
- **Text Tertiary** (`#6A6B70` dark / `#9A9BA0` light): Placeholder, disabled.
- **Reasoning Text** (`#8A8B90` dark / `#6E6F74` light): Chain-of-thought inside the trace — dimmed, italic.

### Semantic
- **Success Green** (`#2EBD85`): "Copied", positive feedback (thumbs-up filled).
- **Error Red** (`#E5484D`): Failed generation, network error, stop.
- **Citation Blue** (`#4D6BFE`): Inline web-search citation chips `[1] [2]` when Search is on.
- **Stop Red** (`#E5484D`): Stop-generating button while streaming.

### Color Usage Rules
| Token | Dark | Light | Where |
|-------|------|-------|-------|
| Canvas | `#0E0E10` | `#FFFFFF` | Behind everything |
| Surface 1 | `#1A1B1E` | `#F5F6F8` | Composer, cards |
| Reasoning BG | `#16171A` | `#F7F8FA` | The reasoning trace panel |
| Divider | `#2E2F33` | `#E4E6EB` | Hairlines, borders |
| DeepSeek Blue | `#4D6BFE` | `#4D6BFE` | Whale, send, active toggle, links |
| Blue Soft | `#1E2240` | `#EBEFFF` | User bubble, active toggle fill |
| Text primary | `#ECECEC` | `#1A1B1E` | Answer, body |
| Reasoning text | `#8A8B90` | `#6E6F74` | Chain-of-thought (italic) |

## 3. Typography Rules

### Font Family
- **Primary**: System sans — **SF Pro Text / SF Pro Display** on iOS; `Inter` (by Rasmus Andersson, SIL OFL) as the open/web fallback. No custom display face.
- **Monospace**: `SF Mono` on iOS (`'SF Mono', ui-monospace, Menlo, monospace`) — code blocks/chips and any verbatim tokens.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter / SF Pro | 32pt | 800 | 1.15 | -0.4pt | App title / large nav |
| Empty-State Headline | Inter / SF Pro | 26pt | 700 | 1.25 | -0.3pt | "How can I help today?" |
| Section Heading | Inter / SF Pro | 18pt | 700 | 1.3 | -0.2pt | History date groups |
| Message Body | Inter / SF Pro | 15pt | 400 | 1.6 | 0pt | User & assistant text |
| Toggle / Action | Inter / SF Pro | 15pt | 600 | 1.0 | 0pt | DeepThink, Search, primary action |
| Card / List Title | Inter / SF Pro | 15pt | 600 | 1.3 | 0pt | History conversation titles |
| Reasoning Trace | Inter / SF Pro | 13pt | 400 *italic* | 1.55 | 0pt | Chain-of-thought — dimmed italic |
| Metadata | Inter / SF Pro | 14pt | 400 | 1.4 | 0pt | "Thought for 14 seconds" |
| Caption | Inter / SF Pro | 12pt | 500 | 1.3 | 0pt | Model name, timestamps |
| Code Inline | SF Mono | 13pt | 400 | 1.4 | 0pt | `inline code` — blue token `#C5CDFF` |
| Code Block | SF Mono | 13pt | 400 | 1.5 | 0pt | Fenced code blocks |
| Citation Chip | SF Mono | 11pt | 600 | 1.0 | 0pt | `[1] [2]` web-search citations |
| Tab Label | Inter / SF Pro | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Placeholder | Inter / SF Pro | 15pt | 400 | 1.4 | 0pt | "Message DeepSeek" |

### Principles
- **Reasoning is italic, answer is upright**: the chain-of-thought is always italic and dimmed (`#8A8B90`); the final answer is upright and full-contrast (`#ECECEC`). The italic *is* the "this is internal monologue" signal — never render the trace upright.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium captions, semibold toggles/actions, bold headings, extrabold title. No thin, no light.
- **One accent, used sparingly**: blue appears on the whale, user bubble, send, active toggles, links, citations — nowhere else. Body text is never blue.
- **Conventional letterforms by design**: personality lives in the reasoning trace and the single blue, not the typeface.
- **Dynamic Type**: message body, headings, reasoning text scale; tab labels, citation chips, toggle text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (New chat / Send action)**
- Shape: Full pill, 999pt corner radius (or 50% circle for the composer send)
- Background: `#4D6BFE` (DeepSeek Blue)
- Text / icon: `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal (pill); 36pt circle for send
- Font: Inter 15–16pt weight 600
- Pressed: background `#3B57E0` + scale 0.98
- Disabled (empty input): background `#232428`, icon `#6A6B70`

**Secondary / Outline Button (Regenerate)**
- Background: transparent
- Text: `#ECECEC`
- Border: 1pt `#2E2F33`
- Corner radius: 999pt
- Padding: 12pt vertical, 20pt horizontal
- Font: Inter 14pt weight 500
- Pressed: background `#1A1B1E`

**Soft Button (Continue reasoning / inline secondary)**
- Background: `#1E2240` (Blue Soft)
- Text / icon: `#4D6BFE`
- Border: 1pt `#2A3360`
- Corner radius: 999pt
- Padding: 10pt vertical, 18pt horizontal
- Font: Inter 14pt weight 600

**Text Button (Clear conversation / Delete)**
- Font: Inter 14pt weight 500
- Color: `#9A9BA0` (or `#E5484D` for destructive)
- No background, no border
- Pressed: color → `#ECECEC`

### Toggle Pills (the core control)

**DeepThink (R1) / Search Toggle**
- Shape: pill, 999pt corner radius
- Off: background `#1A1B1E`, border 1pt `#2E2F33`, text/icon `#9A9BA0`
- On: background `#1E2240` (Blue Soft), border 1pt `#4D6BFE`, text/icon `#4D6BFE`
- Leading icon: 14pt (brain glyph for DeepThink, magnifier for Search)
- Font: Inter 13pt weight 600
- Padding: 7pt vertical, 13pt horizontal
- Transition: 150ms ease — fill + border + content color crossfade
- DeepThink ON ⇒ replies include the reasoning trace; Search ON ⇒ replies include `[n]` citations

### Reasoning Trace (signature component)

The defining element of DeepSeek. When DeepThink (R1) is on, every assistant reply is preceded by this panel.

- Container: `#16171A` (dark) / `#F7F8FA` (light) — intentionally recessed vs. canvas
- Left bar: 2pt solid `#4D6BFE` (DeepSeek Blue), full-height
- Corner radius: 10pt
- Padding: 12pt vertical, 14–16pt horizontal
- Header row: brain glyph (14pt `#4D6BFE`) + "Thought for N seconds" (Inter 12pt w600 `#9A9BA0`) + "· tap to collapse" (`#6A6B70`)
- Body: chain-of-thought in Inter 13pt **w400 italic** `#8A8B90`, line-height 1.55
- Collapsed state: header only (single line); expands with a 200ms height animation + chevron
- While streaming: the trace types in live (token-by-token) with a blinking caret; the duration counts up
- Subordination rule: the trace is always *visually quieter* than the answer below it — dimmer text, recessed background, smaller size

### Message Bubbles

**User Message**
- Background: `#1E2240` (Blue Soft), border 1pt `#2A3360`
- Text: `#ECECEC`, Inter 15pt w400, line-height 1.5
- Corner radius: asymmetric `18 / 18 / 4 / 18` (the 4pt corner points to the sender, bottom-right)
- Padding: 12pt vertical, 16pt horizontal
- Max width: 78% of content width, right-aligned

**Assistant Message**
- No bubble — full content width, left-aligned
- Header: whale glyph (18pt `#4D6BFE`) + model name ("DeepSeek-R1" / "DeepSeek-V3", Inter 13pt w600 `#9A9BA0`)
- Body: `#ECECEC`, Inter 15pt w400, line-height 1.6
- Action row below: copy, thumbs-up, thumbs-down, regenerate, share — 16pt glyphs `#6A6B70`
- Code blocks: `#232428` background, 8pt radius, SF Mono 13pt, blue token `#C5CDFF`, "Copy" affordance top-right

### Composer (signature input)

- Toggle row on top: DeepThink (R1) + Search pills (see Toggle Pills), 8pt gap, 8pt bottom margin
- Input pill: 50pt tall, `#1A1B1E` background, 1pt `#2E2F33` border, 25pt corner radius
- Leading 18pt inset, placeholder "Message DeepSeek" in `#6A6B70`
- Trailing: 36pt circular send button — `#4D6BFE` fill, `#FFFFFF` up-arrow glyph (turns into a red `#E5484D` stop square while streaming)
- Attach (+) affordance optional at the leading edge for image/file upload
- On focus: border brightens to `#3A3B40`

### Navigation

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `rgba(14,14,16,0.94)` with `backdrop-filter: blur(20px)`, 0.5pt top border `#2E2F33`
- Tabs (3): Chat, History, Settings
- Icon size: 22pt
- Active: glyph + label `#4D6BFE` (the single accent), slightly thicker stroke
- Inactive: `#6A6B70`
- Labels: Inter 10pt w500, always shown

**Top Bar**
- Height: 44pt + safe area
- Leading: hamburger / history icon (22pt `#9A9BA0`)
- Center: whale glyph (22pt `#4D6BFE`) + "DeepSeek" wordmark (Inter 16pt w700; "Seek" tinted `#4D6BFE`)
- Trailing: new-chat (+) icon (22pt `#9A9BA0`)

### Input Fields

**Search Bar (History)**
- Height: 44pt
- Background: `#1A1B1E`, no border
- Corner radius: 10pt
- Leading `magnifyingglass` 18pt `#6A6B70`
- Placeholder "Search chats" 14pt `#6A6B70`
- Focus: 1pt `#4D6BFE` border

### Distinctive Components

**Reasoning Trace Panel** — see above; the product's signature.

**DeepThink / Search Toggle Pair** — the primary controls; soft-blue active state.

**Streaming Answer with Live Trace**
- The reasoning panel types in token-by-token first (duration counting up), then the upright answer streams below it. A blinking blue caret marks the active position.

**Web-Search Citation Chips**
- When Search is on, inline `[1] [2]` chips in SF Mono 11pt w600 `#4D6BFE` on a faint `#1E2240` pill; tapping opens a source sheet listing titles + domains.

**Model Switcher**
- A small pill in the empty state / settings to pick DeepSeek-V3 (fast) vs DeepSeek-R1 (reasoning). R1 implies the trace.

**Empty State**
- Centered whale glyph (44pt `#4D6BFE`) + "How can I help today?" (26pt w700) + 3–4 example-prompt chips (`#1A1B1E`, 1pt `#2E2F33`, pill)

**Code Block**
- `#232428` background, 8pt radius, language label top-left (`#6A6B70` 11pt), "Copy" top-right, SF Mono 13pt body with blue-tinted tokens

## 5. Layout Principles

### Spacing System
- Base unit: 4pt — scale: 4, 8, 10, 12, 16, 18, 24, 32, 48
- Message vertical gap: 18pt
- Reasoning-panel internal padding: 12/14pt; gap to answer: 10pt
- Screen horizontal inset: 16pt

### Grid & Container
- iPhone: chat at 16pt horizontal insets; user bubble max 78% right-aligned; assistant full-width
- iPad: chat column centered, max-width 720pt; History becomes a 2-pane (list + chat)
- Composer pinned to bottom; toggle row sits directly above the input pill
- Reasoning panel spans the full assistant content width (no extra inset)

### Whitespace Philosophy
- **Calm and recessive**: surfaces differ by only a few percent luminance; the reasoning panel is *darker* than the canvas so it reads as "behind/internal"
- **Answer-forward**: the most contrast in the screen is on the final answer (`#ECECEC` on `#0E0E10`); reasoning is deliberately lower-contrast
- **One spark of color**: blue is rationed — its rarity is what makes the toggles and citations legible
- **Generous line-height**: 1.6 on the answer for long-form reading; 1.55 on the (smaller) trace

### Border Radius Scale
- Square (0pt): dividers, full-bleed media
- Subtle (4pt): inline code chips, the bubble's sender-pointing corner
- Small (8pt): code blocks, source-sheet rows
- Standard (10pt): reasoning panel, search bar, cards
- Bubble (18pt): user message bubble (3 corners)
- Composer (25pt): the input pill
- Pill (999pt): toggles, primary buttons, citation chips
- Circle (50%): send button, avatars

## 6. Depth & Elevation

DeepSeek is flat and recessive. Depth is conveyed by *recession* (the trace sits visually behind) more than by elevation.

| Level | Treatment | Use |
|-------|-----------|-----|
| Recessed (Level -1) | `#16171A` (darker than canvas) + 2pt blue left-bar | Reasoning trace panel |
| Flat (Level 0) | No shadow | Messages, composer, content |
| Hairline (Level 1) | 1pt `#2E2F33` border | Composer, toggles, cards, code blocks |
| Floating (Level 2) | `rgba(0,0,0,0.5) 0 8px 24px` + 1pt `#2E2F33` | Source sheet, model menu, bottom sheets |
| Scrim (Level 3) | `rgba(0,0,0,0.6)` overlay | Behind modals and sheets |

**Shadow Philosophy**: On near-black, shadows barely register, so a 1pt `#2E2F33` border is the primary "this is a distinct surface" cue. The reasoning trace inverts the usual logic — it uses a *darker* fill (not a shadow) to communicate "this is the model's inner monologue, behind the answer." Sheets get a faint shadow plus a border.

### Motion
- **Toggle on/off**: fill + border + content color crossfade over 150ms ease; soft haptic on engage
- **Reasoning stream**: trace text types in token-by-token (~30–60 tok/s feel), duration label counts up live; a blue caret blinks at the cursor
- **Trace collapse/expand**: chevron rotates 90° in 150ms; panel height animates 0 ↔ auto over 200ms ease-out
- **Answer stream**: upright answer types in below the trace after reasoning completes; same caret hands off
- **Send**: send button scales 1 → 0.94 → 1 (120ms) then morphs to a red stop-square while streaming; reverts on completion
- **Message append**: new user bubble slides up + fades in over 200ms ease-out
- **Citation tap**: source sheet slides up 300ms ease-out
- **Tab change**: instant color swap (no slide)
- **Haptic feedback**: soft impact on toggle engage and send; success haptic on copy

## 7. Do's and Don'ts

### Do
- Use a near-black `#0E0E10` canvas (dark) / clean `#FFFFFF` (light) — quiet research-tool calm
- Ration DeepSeek Blue `#4D6BFE` to the whale, user bubble, send, active toggles, links, citations
- Render the reasoning trace in a recessed `#16171A` panel with a 2pt blue left-bar
- Keep the chain-of-thought dimmed italic `#8A8B90` — italic IS the "internal monologue" signal
- Keep the answer upright and full-contrast `#ECECEC` — visually dominant over the trace
- Make the trace collapsible with a "Thought for N seconds · tap to collapse" header
- Use soft-blue active state (`#1E2240` fill + `#4D6BFE` border) for DeepThink / Search toggles
- Give the user bubble the asymmetric `18/18/4/18` corner (4pt points at the sender)
- Stream the trace token-by-token with a live duration counter, then the answer
- Show `[n]` citation chips when Search is on; open a source sheet on tap

### Don't
- Don't render the reasoning trace upright or full-contrast — it must be dimmer/italic/recessed
- Don't make the trace background lighter than the canvas — it recedes by being *darker*
- Don't use blue for body text — blue is reserved for accents/toggles/links/citations only
- Don't give assistant messages a bubble — only the user gets a bubble; AI is full-width
- Don't add a second accent color — DeepSeek has exactly one (`#4D6BFE`)
- Don't show the trace when DeepThink (R1) is off (V3 fast mode answers directly, no trace)
- Don't use a heavy custom display font — DeepSeek uses the platform sans
- Don't elevate the trace with a shadow — it uses a darker fill, not elevation
- Don't keep the trace expanded by default on long answers — collapse after completion is fine
- Don't tint the active tab with a pill — active is just `#4D6BFE` glyph + label

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max 80%; toggles may wrap to 2 rows |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Wider chat column; larger trace text |
| iPad (portrait) | 768pt | Chat centered max 720pt; History 2-pane |
| iPad (landscape) | 1024pt+ | Persistent History sidebar + chat; trace inline |

### Dynamic Type
- Message body, headings, reasoning text: full scale
- Tab labels, citation chips, toggle text: FIXED (layout-sensitive)
- Code blocks: scale to XL (stay monospace)

### Orientation
- All screens support rotation
- iPad split view: History list (primary) + active chat (secondary); the trace stays inline within the assistant message

### Touch Targets
- Toggle pill: 44pt min hit, 13pt label
- Trace header (collapse/expand): full-width 36pt hit
- Tab bar icons: 22pt glyph, 44pt hit
- Send button: 36pt circle, 44pt hit
- Citation chip: 24pt hit, 11pt glyph

### Safe Area Handling
- Top: top bar respects safe area and Dynamic Island
- Bottom: composer (toggles + input) and tab bar respect home indicator
- Keyboard: chat scrolls above keyboard; composer (with toggle row) docks above the keyboard
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0E0E10` · (light): `#FFFFFF`
- Surface 1: `#1A1B1E` dark / `#F5F6F8` light
- Reasoning BG: `#16171A` dark / `#F7F8FA` light (darker/recessed)
- Divider: `#2E2F33` dark / `#E4E6EB` light
- DeepSeek Blue: `#4D6BFE`
- Blue pressed: `#3B57E0`
- Blue soft: `#1E2240` dark / `#EBEFFF` light
- Text primary: `#ECECEC` dark / `#1A1B1E` light
- Reasoning text: `#8A8B90` dark / `#6E6F74` light (italic)
- Text secondary: `#9A9BA0` dark / `#6A6B70` light
- Success: `#2EBD85` · Error/Stop: `#E5484D`

### Example Component Prompts
- "Build the DeepSeek reasoning trace in SwiftUI: a recessed panel, background `#16171A` (darker than the `#0E0E10` canvas), a 2pt `#4D6BFE` left bar, 10pt corner radius. Header row: brain SF Symbol 14pt `#4D6BFE` + 'Thought for 14 seconds' (Inter 12pt w600 `#9A9BA0`) + '· tap to collapse' (`#6A6B70`). Body: chain-of-thought in Inter 13pt w400 **italic** `#8A8B90`, line-height 1.55. Tap header → 200ms height collapse to a single line with a chevron rotation."

- "Create the DeepSeek composer: a toggle row above a pill input. Two toggle pills — 'DeepThink (R1)' (brain icon) and 'Search' (magnifier) — off = `#1A1B1E` bg / 1pt `#2E2F33` border / `#9A9BA0` text; on = `#1E2240` bg / 1pt `#4D6BFE` border / `#4D6BFE` text+icon, 150ms crossfade. Below: 50pt pill, `#1A1B1E` bg, 1pt `#2E2F33` border, 25pt radius, placeholder 'Message DeepSeek' `#6A6B70`, trailing 36pt circular send (`#4D6BFE` fill, white up-arrow; morphs to a red `#E5484D` stop square while streaming)."

- "Render a DeepSeek assistant message: no bubble, full content width. Header: whale glyph 18pt `#4D6BFE` + 'DeepSeek-R1' (Inter 13pt w600 `#9A9BA0`). If DeepThink is on, a reasoning trace panel precedes the answer. Answer body: Inter 15pt w400 `#ECECEC`, line-height 1.6. Inline code: SF Mono 13pt on `#232428` chip with token color `#C5CDFF`. Action row: copy / thumbs-up / thumbs-down / regenerate, 16pt `#6A6B70`."

- "Build the DeepSeek user bubble: background `#1E2240`, 1pt `#2A3360` border, text `#ECECEC` Inter 15pt w400 line-height 1.5, asymmetric corner radius 18/18/4/18 (4pt corner bottom-right pointing at the sender), 12pt×16pt padding, max width 78% right-aligned."

- "Create the DeepSeek streaming sequence: when DeepThink (R1) is on, first the reasoning panel types in token-by-token with a blinking `#4D6BFE` caret and a live-counting 'Thought for N seconds' label; when reasoning finishes, the upright answer streams below in `#ECECEC` and the caret hands off. While streaming, the send button is a red `#E5484D` stop square."

- "Build the DeepSeek bottom tab bar: 68pt + safe area, `rgba(14,14,16,0.94)` 20px blur, 0.5pt `#2E2F33` top border. Three tabs: Chat, History, Settings — 22pt glyphs, Inter 10pt w500 labels. Active = `#4D6BFE` glyph+label (slightly thicker stroke, NO tint pill). Inactive = `#6A6B70`."

### Iteration Guide
1. Canvas is near-black `#0E0E10` (dark) / clean white `#FFFFFF` (light) — calm, research-tool quiet
2. Exactly one accent: DeepSeek Blue `#4D6BFE` — whale, user bubble, send, active toggles, links, citations; never body text
3. The reasoning trace is THE signature — a recessed `#16171A` panel (darker than canvas) with a 2pt blue left-bar
4. Chain-of-thought is dimmed `#8A8B90` **italic** 13pt; the answer is upright `#ECECEC` 15pt and visually dominant
5. The trace header reads "Thought for N seconds · tap to collapse" — tap collapses with a 200ms height animation
6. DeepThink (R1) toggle ⇒ shows the trace; Search toggle ⇒ adds `[n]` citation chips — both use soft-blue active state
7. User messages get a `#1E2240` bubble with an asymmetric 18/18/4/18 corner; assistant messages have no bubble
8. Stream the trace token-by-token with a live duration counter, THEN the upright answer below it
9. Surfaces differ by only a few % luminance; the trace recedes by being darker, not by a shadow
10. The active tab is just `#4D6BFE` glyph+label — no tinted pill; DeepSeek has no second accent
