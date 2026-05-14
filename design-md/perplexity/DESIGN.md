# Design System Inspiration of Perplexity (iOS)

## 1. Visual Theme & Atmosphere

Perplexity's iOS app is the answer engine made tactile. Where ChatGPT and Claude are chat surfaces optimized for conversation, Perplexity is a search engine optimized for one shape of interaction: ask a question, get a cited answer. The interface reflects this — a near-black canvas (`#0A0A0A`), a single signature cyan-teal accent that Perplexity calls "Sky" or "True Turquoise" (`#20B8CD`-ish, varying slightly across surfaces), and a hierarchy that puts the question at the top, the answer in the middle, and the source list directly underneath. The app reads as a research notebook, not a chat.

The accent — a clean cyan-teal — is Perplexity's most distinctive design choice. It's not the warm orange of ChatGPT or the terracotta of Claude; it's a cool, slightly desaturated turquoise that connotes information, water, clarity, depth. It carries the send button, the active state of the search bar focus ring, the citation chip outlines, and the brand mark — a stylized triple-circle / asterisk that evokes a search field's autocomplete dropdown. Everything else is white type on dark canvas, with a tight ladder of grey for secondary text and chip surfaces. The restraint is intentional: Perplexity is an information tool, and information needs to read clean.

Typography is FK Grotesk Neue and Inter — Perplexity uses both, with FK Grotesk for the brand voice (headings, the model picker, "Sources" labels) and Inter for the bulk of the answer prose. Both are geometric humanist sans serifs; Perplexity deliberately avoids the editorial-essayist serif voice of Claude in favor of a more technical, encyclopedic register. The answer text is sized at 16pt with a moderately tight 1.5 line-height — Perplexity reads like a Wikipedia paragraph, not a Tiempos essay.

**Key Characteristics:**
- Near-black canvas (`#0A0A0A`) with elevated surface (`#171717`) for input fields and source cards
- Perplexity Teal (`#20B8CD`) — cool cyan-teal — as the single signature accent for sends, focus rings, citation outlines, and the brand mark
- Citation chips — small numbered pills (`[1]`, `[2]`, `[3]`) inline in the answer that link to the source list below
- Source card row — horizontal scroll of source cards (favicon + truncated title + domain) at the top of every answer
- The triple-circle / asterisk brand mark in Perplexity Teal — sits on the splash, the empty-state, and the avatar of every answer
- Pro Search toggle — a switch that turns the answer into a multi-step reasoning trace, visible as a "Steps" expandable list above the answer
- Discover / Library / Spaces tabs — a 4-tab bottom nav including the Discover feed of trending searches
- Inter for answer body, FK Grotesk for chrome — clean geometric sans typography
- Hard edges, low elevation, no glass — the app is a tool, not a chat

## 2. Color Palette & Roles

### Primary
- **Perplexity Teal** (`#20B8CD`): The signature accent — send button, focus rings, citation chip outlines, brand mark, "Pro" badge fill, active tab indicator.
- **Teal Bright** (`#3DD6EC`): Hover / pressed state on the send button; the streaming response cursor.
- **Teal Deep** (`#1591A3`): Dark-state variants — pressed Pro badge, secondary teal accents.
- **Teal Soft** (`#0F3A42`): Background of the active source-card hover state on dark; "Searching the web…" indicator soft fill.

### Canvas (dark-first — Perplexity defaults to dark)
- **Canvas** (`#0A0A0A`): Primary near-black canvas — every screen by default.
- **Surface 1** (`#171717`): Input field background, source card fill, modal surfaces.
- **Surface 2** (`#1F1F1F`): Pressed states, hover, chip fills on dark.
- **Surface 3** (`#2A2A2A`): Higher elevation — sheets, sticky headers above content.
- **Divider** (`#2A2A2A`): 0.5pt hairlines on dark.

### Text (on dark)
- **Text Primary** (`#FAFAFA`): Primary text — answer body, question titles, model name.
- **Text Secondary** (`#A1A1A1`): Secondary text — source domains, citation hints, metadata.
- **Text Tertiary** (`#6E6E6E`): Tertiary — placeholder, helper, disabled labels.
- **Text Muted** (`#4A4A4A`): Very-secondary metadata, separator dots.

### Code & Syntax (inside code blocks)
- **Code BG** (`#0E0E0E`): Slightly darker than canvas — code blocks pop subtly
- **Code FG** (`#E5E5E5`): Default code text
- **Syntax Keyword** (`#20B8CD`): Keywords — Perplexity Teal
- **Syntax String** (`#A8E063`): Strings — lime green
- **Syntax Number** (`#F2A65A`): Numbers — soft orange
- **Syntax Comment** (`#6E6E6E`): Comments
- **Syntax Function** (`#B988F2`): Function names — soft purple

### Semantic
- **Success Green** (`#22C55E`): "Copied", citation source verified
- **Warning Amber** (`#F59E0B`): "Limited results", paywall warnings
- **Error Red** (`#EF4444`): Failed queries, source unavailable
- **Pro Gold** (`#E0B341`): "Pro" subscription badges (used sparingly — gold is reserved for premium identity)

### Light Mode
Perplexity supports light mode, but the brand defaults to dark. Light mode preserves all teal accents.
- **Light Canvas** (`#FFFFFF`): Primary light canvas.
- **Light Surface 1** (`#F7F7F7`): Input fields, source cards.
- **Light Surface 2** (`#EFEFEF`): Pressed states, chip fills on light.
- **Light Divider** (`#E5E5E5`): Hairlines on light.
- **Light Text Primary** (`#111111`): Primary text on light.
- **Light Text Secondary** (`#555555`): Secondary.
- **Light Text Tertiary** (`#999999`): Tertiary.
- **Perplexity Teal (light)** (`#1591A3`): Slightly darker teal for WCAG AA on light background.

## 3. Typography Rules

### Font Family
- **Primary (chrome, headings, brand voice)**: `FK Grotesk Neue` — a geometric humanist sans by Florian Karsten, used by Perplexity for the brand identity
- **Body (answer text)**: `Inter` — Rasmus Andersson's geometric humanist sans, used for the bulk of answer prose
- **Weights available**: FK Grotesk: Regular (400), Medium (500), Bold (700); Inter: Regular (400), Medium (500), Semibold (600), Bold (700)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', sans-serif`
- **Google Fonts substitute**: `Inter` for both — both faces have similar geometric humanist characteristics and Inter ships everywhere

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display (Discover hero) | FK Grotesk | 30pt | 700 | 1.15 | -0.5pt | "Discover", "Library", "Spaces" |
| Question Title | FK Grotesk | 22pt | 600 | 1.25 | -0.3pt | The user's question at the top of every answer |
| Section Header | FK Grotesk | 18pt | 600 | 1.3 | -0.1pt | "Sources", "Related", "Steps" |
| Subsection | FK Grotesk | 16pt | 600 | 1.3 | 0pt | "Web", "Reddit", subsection headings inside answers |
| Answer Body | Inter | 16pt | 400 | 1.5 | 0pt | The bulk of the answer prose |
| Answer Body Bold | Inter | 16pt | 600 | 1.5 | 0pt | Emphasis within answers |
| List Item | Inter | 16pt | 400 | 1.5 | 0pt | Bulleted / numbered lists inside answers |
| Source Card Title | Inter | 13pt | 500 | 1.3 | 0pt | Source title in the source row, 2-line truncate |
| Source Card Domain | Inter | 11pt | 400 | 1.3 | 0pt | "wikipedia.org", "arxiv.org" |
| Citation Chip | FK Grotesk Mono | 11pt | 500 | 1.0 | 0pt | The `1` / `2` numerals inside citation chips |
| Search Placeholder | Inter | 16pt | 400 | 1.4 | 0pt | "Ask anything…" |
| Button (Primary) | FK Grotesk | 15pt | 600 | 1.0 | 0pt | "Search", "Save to Library" |
| Button (Pro) | FK Grotesk | 13pt | 700 | 1.0 | 0.2pt | "Pro" badge label |
| Tab Label | FK Grotesk | 10pt | 500 | 1.0 | 0.2pt | Bottom tab labels |
| Metadata | Inter | 12pt | 400 | 1.3 | 0pt | Timestamps, "12 sources" |
| Code Block | JetBrains Mono | 14pt | 400 | 1.5 | 0pt | Code inside answers |
| Inline Code | JetBrains Mono | 14pt | 500 | 1.4 | 0pt | `inline` code |
| Caption | Inter | 11pt | 400 | 1.3 | 0pt | "Powered by Perplexity Pro" |

### Principles
- **Two-face system**: FK Grotesk carries the chrome and brand voice; Inter carries the answer body. The split is intentional — the chrome speaks "Perplexity", the body speaks "encyclopedia".
- **Answer body at 16pt with 1.5 line-height**: This is the most-read text in the app, and it's intentionally tighter than Claude's 1.55. Perplexity reads denser, more reference-book.
- **Numeric citations are typeset matter**: `[1]`, `[2]`, `[3]` use FK Grotesk Mono — a monospace variant — at 11pt 500 to ensure they hold their pill shape and align in lists.
- **Off-white primary text, never pure white**: `#FAFAFA` reads softer on near-black canvas than pure `#FFFFFF`; reduces glare and feels more typographic.
- **Dynamic Type respected on answer body, headings, lists**: Citation chips, tab labels, source domain text, and code block language labels are fixed (layout-sensitive).
- **Tight tracking at display**: -0.5pt at 30pt falling to 0pt at body — geometric sans needs slight negative tracking at headlines to feel composed.

## 4. Component Stylings

### Buttons

**Send / Search Circle (the teal icon-only send)**
- Diameter: 36pt
- Background: `#20B8CD` (Perplexity Teal) when input has text; `#2A2A2A` (Surface 3) when empty
- Icon: 14pt arrow-up glyph, `#0A0A0A` when active (dark text on teal), `#6E6E6E` when disabled
- Pressed: scale 0.94, background `#3DD6EC`
- Haptic: `.impactOccurred(.medium)` on send
- Sits trailing the search input

**Primary Pill (Save to Library, Sign in, etc.)**
- Background: `#20B8CD` Perplexity Teal
- Text: `#0A0A0A` (dark on teal for WCAG)
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 10pt (medium — Perplexity avoids capsule corners outside of citation chips)
- Pressed: background `#1591A3`

**Secondary Pill (Copy, Share, Pro Search)**
- Background: `#1F1F1F` (Surface 2)
- Border: 1pt `#2A2A2A`
- Text: `#FAFAFA`, FK Grotesk 14pt Medium
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 8pt

**Pro Search Toggle (the multi-step reasoning toggle)**
- Pill-shaped switch sitting above the search input
- Off state: `#1F1F1F` fill, "Pro" label in `#A1A1A1` FK Grotesk 13pt Medium, no fill
- On state: `#20B8CD` Perplexity Teal fill, "Pro" label in `#0A0A0A` FK Grotesk 13pt Bold
- 28pt tall, 60pt wide, 14pt radius
- A small `sparkle` glyph leads the "Pro" text on hover
- Tap toggles with a `.selectionChanged()` haptic + a 200ms slide animation on the indicator

**Citation Chip (the most-rendered button in the app)**
- Inline within answer text — replaces what would otherwise be a footnote
- Width: auto, min 18pt; height: 18pt
- Background: `#1F1F1F` (Surface 2) — or `#0F3A42` (Teal Soft) when hovered/focused
- Border: 1pt `#2A2A2A` default; 1pt `#20B8CD` Perplexity Teal when focused
- Text: `[1]`, `[2]`, `[3]` — number only, FK Grotesk Mono 11pt Medium, color `#A1A1A1` default / `#20B8CD` on focus
- Padding: 0pt vertical (height controlled by font), 5pt horizontal
- Corner radius: 4pt
- Tap: scrolls to / opens the matching source card; brief `.impactOccurred(.soft)` haptic
- Hover (iPad): reveals a tooltip with the source title + favicon

### Cards & Containers

**Search Input (the home of the app)**
- Width: full minus 24pt margins
- Background: `#171717` (Surface 1)
- Border: 1pt `#2A2A2A` default; 1.5pt `#20B8CD` focused
- Corner radius: 24pt (capsule-ish on single line; softer when grown)
- Min height: 56pt; auto-grow up to 200pt
- Padding: 14pt vertical, 18pt left, 60pt right (room for send circle)
- Placeholder: "Ask anything…" in Inter 16pt Regular `#6E6E6E`
- Text: Inter 16pt Regular `#FAFAFA`
- Trailing: paperclip-attach button + 36pt send circle stacked horizontally
- Below the input (when empty): a row of suggested-prompts chips ("Trending", "Compare", "Summarize this URL") — pills with `#1F1F1F` fill, `#A1A1A1` 13pt Medium text

**Source Card (horizontal scroll above answer)**
- Width: 200pt × 80pt
- Background: `#171717` (Surface 1)
- Border: 1pt `#2A2A2A`
- Corner radius: 12pt
- Padding: 12pt all around
- Top row: 16pt favicon + Inter 11pt Regular `#A1A1A1` domain ("wikipedia.org")
- Bottom: Source title in Inter 13pt Medium `#FAFAFA`, 2-line truncate with ellipsis
- A small numeric badge top-right (`1`, `2`, `3`) matching the inline citation — `#1F1F1F` fill with the number in `#A1A1A1` FK Grotesk Mono 10pt Bold
- Horizontal scroll row, 12pt gap between cards, 12pt leading inset
- After the last card, a "Show all 12 sources" chevron card with `#1F1F1F` fill

**Answer Block (the conversation hero)**
- Full-width flow of typography — no container background, no bubble
- Top: a 20pt Perplexity-mark icon at the leading edge + "Answer" label in FK Grotesk 13pt Medium `#A1A1A1`
- Below: the answer body in Inter 16pt Regular `#FAFAFA` with 1.5 line-height
- Citation chips inline within the prose, looking like superscript-but-pill — `[1]`, `[2]` etc.
- Markdown rendering: headings get FK Grotesk Semibold treatment (h2 18pt, h3 16pt, h4 14pt); lists get 8pt gap; bold/italic/inline-code distinct
- Action row at the bottom of the answer: Copy / Share / Pro Search retry / Save to Library — secondary pill buttons
- 32pt gap between the answer block and the related-questions section below

**Related Questions Card (under every answer)**
- "Related" label in FK Grotesk 13pt Medium `#A1A1A1` at the top
- A vertical stack of 3-5 question rows
- Each row: 56pt tall, full width, 1pt `#2A2A2A` bottom border, padding 12pt vertical / 16pt horizontal
- Row content: question text in Inter 15pt Regular `#FAFAFA` + a 14pt `arrow.right` chevron in `#A1A1A1` trailing
- Tap: starts a new search with that question
- Hover/press: `#1F1F1F` fill on the row

**Pro Steps List (when Pro Search is on)**
- A collapsible card above the answer
- Header: 14pt `sparkle` glyph in `#20B8CD` Perplexity Teal + "Steps" label in FK Grotesk 13pt Medium `#FAFAFA` + step count "(4)" in `#A1A1A1`
- Each step: a row showing the step name (e.g., "Searching for: 'Bayesian inference history'") + a small completed-check `#20B8CD` glyph
- Collapsed by default; tap header to expand with a 300ms ease accordion
- Background: `#0F3A42` Teal Soft, 12pt corner radius, 16pt internal padding

**Code Block**
- Background: `#0E0E0E` (Code BG — slightly darker than canvas to pop)
- Border: 1pt `#2A2A2A`
- Corner radius: 8pt
- Padding: 14pt all around
- Header: language name in JetBrains Mono 11pt `#6E6E6E` left-aligned + Copy button right-aligned, 1pt `#2A2A2A` bottom divider
- Body: JetBrains Mono 14pt Regular `#E5E5E5`
- Syntax: Perplexity Teal keywords, lime strings, soft orange numbers, soft purple functions, grey comments

### Navigation

**Top Header / Search Bar (fixed on chat screens)**
- Height: 56pt
- Background: `#0A0A0A` (matches canvas); no blur on scroll
- Leading: hamburger sidebar toggle (3-line glyph, `#FAFAFA`)
- Center: the active conversation title (Inter 15pt Medium `#FAFAFA`, 1-line truncate) — or absent on the home screen
- Trailing: "..." overflow menu

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#0A0A0A` with 0.5pt `#2A2A2A` top divider
- Tabs (typically 4): Home (search mark), Discover (compass), Library (bookmark), Spaces (collection)
- Active color: `#FAFAFA` (off-white) with a 2pt `#20B8CD` Perplexity Teal indicator dot 4pt below the label
- Inactive: `#6E6E6E`
- Icon size: 22pt, outlined default / filled when active
- Labels: FK Grotesk 10pt Medium, visible always
- Tap haptic: `.selectionChanged()` on each switch

**Sidebar (Conversation History)**
- Slides in from the left on iPhone (full-screen modal); persistent on iPad (260pt width)
- Background: `#0A0A0A`
- Top: "+ New thread" button — full width, `#171717` fill, 1pt `#2A2A2A` border, 10pt corner radius, 12pt vertical padding
- Below: list of "threads" — Perplexity calls a conversation a thread — grouped by date
- Group headers: FK Grotesk 11pt Bold UPPERCASE `#6E6E6E`, 0.4pt letter spacing
- Each thread row: 56pt tall, title in Inter 14pt Medium `#FAFAFA` (1-line truncate), timestamp in Inter 11pt Regular `#A1A1A1` below

### Input Fields

**Settings Text Field**
- Background: `#171717` (Surface 1)
- Border: 1pt `#2A2A2A` default, 1.5pt `#20B8CD` focused
- Corner radius: 8pt
- Height: 48pt
- Padding: 12pt vertical, 14pt horizontal
- Label: above the field in FK Grotesk 13pt Medium `#A1A1A1`

**File / URL Attach Sheet**
- Bottom sheet, 24pt top corners, `#171717` background
- Options: "Upload file", "Paste URL", "Take a photo", each as a 56pt row with a 24pt icon leading + Inter 16pt Medium `#FAFAFA` label

### Distinctive Components

**The Perplexity Mark**
- A stylized triple-circle / asterisk glyph — three overlapping circles arranged in a triangular formation, evoking both an autocomplete dropdown and a Venn diagram intersection
- Rendered in `#20B8CD` Perplexity Teal on dark; `#1591A3` Teal Deep on light
- Sizes: 20pt as the "answer" avatar, 32pt in the search-bar focus state, 80pt on the splash screen
- Never rendered in any color other than the teal scale

**Streaming Response Cursor**
- A small teal caret at the trailing edge of the streaming answer
- Dimensions: 6pt × 16pt, `#3DD6EC` (Teal Bright)
- Animation: blinks at 500ms interval — slightly faster than Claude's 600ms because Perplexity's responses feel more "live"
- Disappears when the answer is complete

**Searching the Web Indicator**
- A loading state shown immediately after submission, before the source cards appear
- A horizontal row of three pulsing teal dots, FK Grotesk 13pt Medium `#A1A1A1` "Searching the web…" label trailing
- Each dot pulses scale 1.0 → 1.3 → 1.0 in sequence with a 200ms stagger, 1200ms cycle
- Replaced by the source card row when results return

**Pro Badge**
- A small teal pill that sits next to "Perplexity Pro" labels in upgrade prompts
- `#20B8CD` Perplexity Teal fill, "Pro" in FK Grotesk 11pt Bold `#0A0A0A`, padded 2pt vertical / 6pt horizontal
- Corner radius: 4pt
- The premium/billing flows use a gold-teal hybrid: `#E0B341` Pro Gold pill with teal accent border

**Trending Search Card (Discover feed)**
- A vertical card with a hero image (4:3 aspect, 12pt rounded top corners), title in FK Grotesk 18pt Semibold `#FAFAFA`, summary in Inter 14pt Regular `#A1A1A1` 2-line truncate
- Source/category footer: small text "Tech · 12 hours ago" in Inter 11pt Regular `#6E6E6E`
- The Discover tab is a 1-col vertical scroll of these cards, no grid

**Spaces Card (collections)**
- Spaces is Perplexity's "save threads to a topic" feature
- Card: `#171717` fill, 12pt corner radius, 16pt padding, 96pt tall
- Top row: Space name in FK Grotesk 16pt Semibold + a thread count "12 threads" in `#A1A1A1`
- Below: a 4-up grid of mini source-favicons representing the sources cited across the space
- Tap opens the Space view — a filtered library of threads

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64
- Standard horizontal margin: 16pt on lists, 24pt on the search input
- Vertical gap between answer block and related questions: 32pt
- Source card row: 12pt gap between cards

### Grid & Container
- Content width: full minus 16pt margins on iPhone; max 720pt centered on iPad
- Sidebar: 260pt fixed width on iPad; full-screen modal on iPhone
- Answer body: full-width flow, no max-width constraint on iPhone
- Source cards: fixed 200pt width regardless of device

### Whitespace Philosophy
- **Dense reference layout**: Perplexity is information-dense — the answer body uses 1.5 line-height (tighter than Claude's 1.55) and lists are tightly packed
- **Source cards earn their margins**: 12pt gap between source cards is generous because the favicon + title + domain stack needs to feel scannable
- **Tight chrome**: the top header (56pt) and tab bar (56pt + safe area) are minimal; the search input is the visual anchor

### Border Radius Scale
- Square (0pt): rarely used
- Small (4pt): citation chips, Pro badge
- Medium (8pt): code blocks, secondary pills, settings inputs
- Standard (10pt): primary pills, sidebar buttons, "Save to Library" CTAs
- Cards (12pt): source cards, Pro Steps card, Spaces cards
- Round Input (24pt): search input field — the only place this radius appears
- Capsule (28pt): the Pro Search toggle
- Sheet (24pt top corners): bottom sheets
- Circle (50%): send button, brand mark container, indicator dots

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, answer body |
| Subtle (Level 1) | `rgba(0, 0, 0, 0.4) 0 1px 2px` | Source cards, code blocks |
| Card (Level 2) | `rgba(0, 0, 0, 0.5) 0 4px 8px` | Pro Steps card, Spaces cards |
| Floating (Level 3) | `rgba(0, 0, 0, 0.6) 0 4px 16px` | Search input on focus, sidebar |
| Overlay (Level 4) | `rgba(0, 0, 0, 0.7) 0 8px 24px` | Bottom sheets, modals |
| Teal Focus Glow | `rgba(32, 184, 205, 0.25) 0 0 0 4px` | Search input focus ring; the only "glow" in the app |

**Shadow Philosophy**: Perplexity on dark canvas needs a different shadow vocabulary than light-mode apps — black-on-black requires high opacity to be visible. Shadows use 0.4-0.7 opacity with short blurs (1-24pt). The teal focus glow on the search input is the single "elevated" visual moment — a soft 4pt cyan halo around the focused search field that signals "this is the action".

### Motion
- **Send tap**: 36pt circle scales 1.0 → 0.94 → 1.0 over 200ms, `.impactOccurred(.medium)` haptic; then the question slides up and the "Searching the web…" indicator fades in over 250ms
- **Source card appear**: when results return, the source card row slides up from below with a 350ms ease-out and a 60ms stagger between cards (so they cascade in)
- **Citation chip tap**: scroll-to-source with a 400ms ease-out scroll animation; the target source card flashes a 200ms `#20B8CD` border glow on arrival
- **Streaming text appearance**: word-by-word fade-in at 60ms per word — slightly faster than Claude's 80ms because Perplexity's prose is denser
- **Streaming cursor blink**: 500ms interval
- **Pro Search toggle**: indicator slides 200ms ease-out between off/on states; the "Pro" label crossfades color
- **Pro Steps expand**: 300ms accordion ease; each step row reveals with a 30ms stagger
- **"Searching…" dot pulse**: 3 dots scale-pulse with 200ms stagger, 1200ms cycle
- **Tab switch**: 200ms ease, indicator dot fades; `.selectionChanged()` haptic
- **Sidebar slide (iPhone)**: 350ms ease with a 0 → 1 dim overlay

## 7. Do's and Don'ts

### Do
- Use the near-black canvas (`#0A0A0A`) as the default — Perplexity's brand is dark-first
- Reserve Perplexity Teal (`#20B8CD`) for the send button, focus rings, citation chip outlines, brand mark, and active states
- Render citation chips inline within the answer prose as `[1]`, `[2]`, `[3]` — never as a footer-only mechanism
- Use FK Grotesk for chrome and brand voice; Inter for answer body
- Use off-white (`#FAFAFA`) for primary text, not pure white — softer on dark canvas
- Render the source card row above every answer with horizontal scroll, 12pt gap, 200pt fixed-width cards
- Use the Perplexity triple-circle mark in teal — never any other color
- Show the "Searching the web…" indicator immediately on submit, before sources arrive
- Use the teal focus glow on the search input — `rgba(32, 184, 205, 0.25) 0 0 0 4px`
- Use dark text (`#0A0A0A`) on Perplexity Teal CTAs — teal needs dark contrast for WCAG AA
- Add a Pro badge in teal on subscription touchpoints, with optional Pro Gold accent on premium upsell

### Don't
- Don't use a chat-bubble pattern with tails for the question or the answer — Perplexity is search, not chat
- Don't use serif typography for the answer body — Perplexity's voice is encyclopedic, not editorial
- Don't replace the triple-circle mark with a generic search glyph or a stylized "P"
- Don't put citation chips only at the end of the answer — they live inline within the prose
- Don't show the answer without the source card row above it — the citation pattern is the brand
- Don't use a warm accent like orange or red — Perplexity Teal is cool, informational; warm accents break the visual register
- Don't blur the canvas with heavy glass — Perplexity is information-first, not chrome-first
- Don't use pure white `#FFFFFF` for primary text — `#FAFAFA` off-white is the correct value on dark
- Don't show Pro Search toggle in the off-state without the "Pro" label — the affordance must be discoverable
- Don't use Perplexity Teal for body text — it's a verb, not a color
- Don't skip the streaming cursor — Perplexity's "live answer" feel depends on it

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Source card width drops 200pt → 180pt; the related questions card uses 1-line truncate |
| iPhone 13/14/15 | 390pt | Standard 200pt source cards, 24pt search input margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; the 56pt header sits below status bar |
| iPhone 15/16 Pro Max | 430pt | Source cards can grow to 220pt; answer body gains slight extra horizontal margin |
| iPad | 768pt+ | Persistent 260pt sidebar; answer max-width 720pt centered; source card row keeps 200pt cards but adds scroll arrows |
| iPad Pro 12.9 | 1024pt+ | Two-column layout: answer left (60%), sources expanded right (40%) — the source list moves from a horizontal row to a vertical sidebar |

### Dynamic Type
- Answer body, all headings, list items, source titles, question titles: full scale
- Citation chips, source domain text, tab labels, code block language label: fixed
- Code block font scales but caps at 18pt to prevent overflow

### Orientation
- All screens: portrait + landscape supported
- On landscape iPhone: sidebar opens as a 60%-width panel, not full-screen
- On iPad: sidebar persistent both orientations; the source list switches between horizontal (portrait) and vertical (landscape) when in two-column mode

### Touch Targets
- Send circle: 36pt button on a 56pt hit area
- Citation chip: 18pt visual on a 32pt hit area (tap-target padding)
- Source card: 200pt × 80pt full-card tap
- Tab bar icons: 22pt icon, full 56pt row
- Related question row: full 56pt row

### Safe Area Handling
- Top: safe area honored — the 56pt header sits below status bar / Dynamic Island
- Bottom: search input pins above home indicator with full safe area inset; tab bar sits above home indicator
- Sides: 16-24pt content insets depending on screen

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas: `#0A0A0A`
- Surface 1 (inputs, cards): `#171717`
- Surface 2 (chips): `#1F1F1F`
- Surface 3 (sheets): `#2A2A2A`
- Divider: `#2A2A2A`
- Text Primary: `#FAFAFA`
- Text Secondary: `#A1A1A1`
- Text Tertiary: `#6E6E6E`
- Perplexity Teal: `#20B8CD`
- Teal Bright (cursor, hover): `#3DD6EC`
- Teal Deep (pressed): `#1591A3`
- Teal Soft (Pro Steps fill): `#0F3A42`
- Code BG: `#0E0E0E`
- Syntax string: `#A8E063` (lime)
- Syntax number: `#F2A65A`
- Syntax function: `#B988F2`
- Pro Gold (premium): `#E0B341`
- Success: `#22C55E`
- Error: `#EF4444`
- Light canvas: `#FFFFFF`
- Light text primary: `#111111`
- Light teal: `#1591A3`

### Example Component Prompts
- "Create a SwiftUI Perplexity search input: full-width minus 24pt margins, 56pt min height, auto-grows to 200pt. `#171717` Surface 1 background, 1pt `#2A2A2A` border default and 1.5pt `#20B8CD` Perplexity Teal border on focus. 24pt corner radius. Placeholder 'Ask anything…' in Inter 16pt Regular `#6E6E6E`. Trailing the input: a 28pt paperclip attach button + a 36pt send circle. Send circle: `#20B8CD` Perplexity Teal when input has text, `#2A2A2A` Surface 3 when empty; 14pt arrow-up glyph in `#0A0A0A` (dark on teal for WCAG). On focus, the input gains a 4pt teal halo `rgba(32, 184, 205, 0.25) 0 0 0 4px`. Send haptic: `.impactOccurred(.medium)`."
- "Build the Perplexity source card row: a horizontal scroll above every answer. Each card 200pt × 80pt, `#171717` Surface 1 background, 1pt `#2A2A2A` border, 12pt corner radius, 12pt padding. Top row: 16pt favicon image + Inter 11pt Regular `#A1A1A1` domain text ('wikipedia.org'). Bottom: Inter 13pt Medium `#FAFAFA` source title, 2-line truncate. A small badge in the top-right corner: `#1F1F1F` Surface 2 background, FK Grotesk Mono 10pt Bold `#A1A1A1` number ('1', '2', '3') matching the inline citation. 12pt gap between cards, 16pt leading inset. After the last source card, a 'Show all 12 sources' chevron-card with `#1F1F1F` fill."
- "Design the Perplexity citation chip: an inline pill that lives within answer prose. Auto-width with 5pt horizontal padding, 18pt tall, 4pt corner radius. Background `#1F1F1F` Surface 2 with a 1pt `#2A2A2A` border default; on focus the border becomes 1pt `#20B8CD` Perplexity Teal and the background shifts to `#0F3A42` Teal Soft. Inside: number text in FK Grotesk Mono 11pt Medium `#A1A1A1` default, `#20B8CD` on focus, with brackets `[1]`. The chip is wrapped in a 32pt tap target via hit-slop padding. Tap scrolls to the matching source card with a 400ms ease-out and a 200ms teal border flash on arrival."
- "Create the Perplexity 'Pro Steps' card: a collapsible card above the answer when Pro Search is on. `#0F3A42` Teal Soft background, 12pt corner radius, 16pt internal padding. Header row: 14pt `sparkles` glyph in `#20B8CD` Perplexity Teal + 'Steps' label in FK Grotesk 13pt Medium `#FAFAFA` + step count '(4)' in `#A1A1A1`. Collapsed by default — a 14pt down-chevron trails the header. Tap header to expand with a 300ms accordion ease. Each step row: 12pt vertical padding, step name in Inter 14pt Regular `#FAFAFA` (e.g., 'Searching for: Bayesian inference history'), with a 14pt `checkmark.circle.fill` glyph in `#20B8CD` trailing when the step is complete."
- "Build the Perplexity related questions card: appears 32pt below every answer. Top row: 'Related' label in FK Grotesk 13pt Medium `#A1A1A1`. Below, a vertical stack of 3-5 question rows. Each row: 56pt tall, full width, 1pt `#2A2A2A` bottom border (only between rows), 12pt vertical / 16pt horizontal padding. Question text in Inter 15pt Regular `#FAFAFA`, 1-line truncate. Trailing: 14pt `arrow.right` chevron in `#A1A1A1`. Hover/press: row background fills `#1F1F1F` Surface 2. Tap starts a new search thread with that question. Haptic: `.selectionChanged()` on tap."

### Iteration Guide
1. Canvas is `#0A0A0A` near-black — Perplexity is dark-first
2. Perplexity Teal `#20B8CD` is the single accent — send button, focus rings, citation outlines, brand mark, active state
3. Two-face typography: FK Grotesk for chrome and brand voice; Inter for answer body
4. Off-white text `#FAFAFA`, never pure white — softer on dark canvas
5. Citation chips `[1]` `[2]` live inline within answer prose with FK Grotesk Mono — the inline citation pattern is the brand
6. Source card row above every answer — 200pt × 80pt cards, horizontal scroll, favicon + domain + title + number badge
7. The triple-circle Perplexity mark stays teal — never any other color, never replaced
8. Pro Search adds a "Steps" card showing the multi-step reasoning trace — Teal Soft fill, collapsible
9. Search input gets a 4pt teal focus halo — the only "glow" in the app
10. Light mode flips canvas to white and darkens teal to `#1591A3` for WCAG; everything else preserved
