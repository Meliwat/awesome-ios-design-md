# Design System Inspiration of Bear (iOS)

## 1. Visual Theme & Atmosphere

Bear's iOS app is a writer's instrument disguised as a design system. The whole product is built around one idea: *the prose is the interface*. Open a note and you see a calm canvas — pure white (`#FFFFFF`) in light, warm blue-charcoal (`#21252B`) in the default dark theme — a 24pt note title, then a flowing column of Markdown text that renders *live*. You type `# Heading` and the hash dims while the line grows bold; you type `**bold**` and the asterisks fade as the word thickens; you type `- [ ] task` and a tappable checkbox appears in place of the brackets. There are no panels, cards, borders or shadows around the writing. The editor is a single, uninterrupted text column with generous 22pt side margins and a comfortable 1.55 line height, tuned for long-form reading rhythm rather than dense UI.

The one piece of unmistakable Bear identity is the **#hashtag**. Bear has no folders — organization is entirely tag-driven. Type `#work/sprint` anywhere in a note and it becomes an inline, tappable, red-tinted token; nested tags (`#work/sprint`) build a hierarchical sidebar automatically. The hashtag *is* Bear's navigation model, and it is rendered in the brand's signature red (`#E0566F`) everywhere it appears — in the body text, in the tag sidebar, and as the section headers of the note list. The other signature moment is the **red→orange gradient** (`#E0566F → #FF8A65`): it is the app icon, the compose-note button, the completed-checkbox fill, the selection accent, and the only saturated color in the entire product. Bear deliberately has *one* accent — everything else is greyscale typography.

The structural model is three panes that collapse responsively: a **tag sidebar** (the hashtag tree + smart filters like Untagged, Todo, Today, Archive, Trash), a **note list** (title + 2-line preview + relative date), and the **editor** itself. On iPhone these are pushed-navigation screens; on iPad they sit side-by-side. Typography is user-switchable in settings across a curated set of faces — Bear ships its own *Avenir Next*-class sans for prose, a serif option for literary notes, and a monospace for code-forward writers. This guide maps those three roles to **Inter** (default sans), **Lora** (serif option), and **JetBrains Mono** (code blocks and mono theme) — all free, SIL OFL fonts that match Bear's reading-first hierarchy.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / warm blue-charcoal (`#21252B`) dark — the prose is the interface, minimum chrome
- Live Markdown rendering — syntax markers (`#`, `**`, `- [ ]`) dim/disappear as styling applies, in-place, no preview pane
- Inline #hashtags as the *entire* organization model — no folders; nested tags build the sidebar tree automatically
- Red→orange gradient (`#E0566F → #FF8A65`) is the *single* brand accent — icon, compose FAB, checkbox fill, selection
- Three-pane responsive layout: tag sidebar → note list → editor (pushed nav on iPhone, side-by-side on iPad)
- User-switchable typography — sans (Inter) / serif (Lora) / mono (JetBrains Mono), applied globally
- Markdown checkboxes, quotes (orange left rule), fenced code blocks, inline code, links — all rendered live
- Multiple shipped themes (Charcoal, Red Graphite, Solarized, Dark Graphite…) — Charcoal `#21252B` is the default dark
- Note list rows: title 17pt semibold + 14pt 2-line preview + relative date, tag-colored section headers
- Bottom info bar in the editor: live word/character count + export, link, pin, info actions
- Minimal navigation chrome: back-chevron + 2–3 trailing glyphs, all in Bear Orange `#FF8A65`

## 2. Color Palette & Roles

### Primary (Interactive)
- **Bear Red** (`#E0566F`): The brand anchor — #hashtag text, note-list section headers, gradient start, destructive emphasis.
- **Bear Orange** (`#FF8A65`): Gradient end, all interactive glyphs (nav icons, info-bar actions), quote left-rule, link-secondary accents.
- **Bear Gradient** (`#E0566F → #FF8A65`, 135°): Compose-note button, completed-checkbox fill, app icon, selection highlight.
- **Bear Red Pressed** (`#C8485E`): Pressed state for red elements and gradient buttons (apply via 0.92 brightness).
- **Link Blue** (`#5AAFEF`): Hyperlinks and `[[wiki-links]]` — the only non-warm interactive color, kept for link legibility.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light editor canvas — paper.
- **Surface Gray** (`#F6F6F4`): Tag sidebar, note-list background, selected-row hover.
- **Surface Pressed** (`#ECECE8`): Pressed/selected note-list row.
- **Divider** (`#E6E6E2`): Hairline between note-list rows and editor info bar.

### Canvas & Surfaces (Dark Mode — "Charcoal" default)
- **Charcoal Canvas** (`#21252B`): Primary dark canvas — Bear's signature warm blue-charcoal, NOT pure black.
- **Dark Surface 1** (`#282C34`): Tag sidebar, note list, code-block background, elevated surfaces.
- **Dark Surface 2** (`#2F343D`): Pressed rows, secondary chips.
- **Dark Divider** (`#353A42`): Hairline dividers and the info-bar top border.

### Text
- **Text Primary Light** (`#2A2C33`): Primary body prose on white — warm near-black, NOT pure black.
- **Text Secondary Light** (`#6B7280`): Note preview, metadata, dimmed Markdown syntax markers.
- **Text Tertiary Light** (`#9AA0AB`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#E8EAED`): Primary body prose on charcoal.
- **Text Secondary Dark** (`#9DA3AD`): Preview, metadata on dark.
- **Text Tertiary Dark** (`#6B7280`): Dimmed syntax markers, disabled (works both modes).

### Tag / Highlight Accents
Bear renders different tag families and inline highlights in a small accent set. These appear as tag-chip text, highlight backgrounds, and note-list section labels.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Tag Red (default) | `#E0566F` | `#E0566F` |
| Tag Blue | `#3F9AE0` | `#5AAFEF` |
| Tag Green | `#3FB873` | `#57C98B` |
| Tag Yellow | `#E0A92E` | `#F2C14E` |
| Tag Purple | `#9B6FE0` | `#B08CF0` |
| Highlight Yellow (bg) | `#FFF1B8` | `#4A431F` |

### Semantic
- **Error Red** (`#F2545B`): Delete confirmation, sync-failure banner.
- **Success Green** (`#3FB873` / `#57C98B`): Sync-success toast, restored-from-trash confirmation.
- **Warning Orange** (`#FF8A65`): Conflict / unsynced-changes warning (reuses Bear Orange).
- **Selection Tint** (`#E0566F26`): ~15% red overlay on selected text in the editor.

## 3. Typography Rules

### Font Family
- **Default (Sans)**: `Inter` (by Rasmus Andersson, SIL OFL) — humanist UI sans standing in for Bear's prose face; superb at long-form reading sizes.
- **Serif Option**: `Lora` (Google Fonts, SIL OFL) — calligraphic transitional serif for literary notes and the serif theme.
- **Mono / Code**: `JetBrains Mono` (by JetBrains, SIL OFL) — code blocks, inline code, and the monospace theme.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **User-Switchable in Settings**: Sans (Inter) / Serif (Lora) / Mono (JetBrains Mono) — applied to *all* notes globally; code blocks always render mono regardless.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| App / Large Title | Inter | 32pt | 800 | 1.2 | -0.6pt | Settings / large nav titles |
| Note Title (`# `) | Inter | 24pt | 700 | 1.25 | -0.3pt | First line of every note; H1 |
| Heading 2 (`## `) | Inter | 19pt | 700 | 1.3 | -0.2pt | In-note section heading |
| Heading 3 (`### `) | Inter | 17pt | 700 | 1.35 | -0.1pt | Sub-section |
| Subhead | Inter | 17pt | 600 | 1.35 | 0pt | Note-list row title |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Paragraph prose default |
| Body (Dense) | Inter | 15pt | 400 | 1.5 | 0pt | Compact line-spacing setting |
| Quote (`> `) | Lora | 17pt | 400 italic | 1.5 | 0pt | Blockquote — orange left rule |
| Preview | Inter | 14pt | 400 | 1.4 | 0pt | Note-list 2-line preview |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | Edited date · word count |
| Tag / #hashtag | Inter | 14pt | 500 | 1.4 | 0pt | Inline tag token + sidebar |
| Code Inline | JetBrains Mono | 14pt | 400 | 1.4 | 0pt | `inline code`, `#282C34` chip |
| Code Block | JetBrains Mono | 13pt | 400 | 1.55 | 0pt | Fenced ``` blocks |
| Caption | Inter | 12pt | 500 | 1.3 | 0.1pt | Info-bar word/char count |
| Tab / Filter Label | Inter | 13pt | 500 | 1.3 | 0.1pt | Sidebar smart-filter rows |

### Principles
- **Reading rhythm, not UI rhythm**: sizes follow editorial convention (title → H2 → H3 → body → quote), not product chrome. Body line height is a generous 1.55.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular prose, medium for tags, semibold for list titles, bold for headings. No thin, no black.
- **Live Markdown is the rule**: never show a separate preview. Render styling in-place; dim the syntax marker (`#`, `**`, `- [ ]`) to `#6B7280` rather than hiding the structure entirely.
- **User controls family**: respect the chosen face (Sans/Serif/Mono) globally; only fenced code is always JetBrains Mono.
- **Monospace is for code only** (and the mono theme): inline code and code blocks get JetBrains Mono; nothing else does in the default theme.
- **Dynamic Type first-class**: title, headings, body, quote, preview, meta scale; tab labels, info-bar caption, tag tokens stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (New Note / compose FAB)**
- Shape: rounded rectangle, 12pt corner radius (the FAB is a 56pt circle)
- Background: linear gradient `#E0566F → #FF8A65` at 135°
- Text/Icon: `#FFFFFF`
- Padding: 13pt vertical, 26pt horizontal
- Font: Inter 16pt weight 600
- Pressed: brightness 0.92 + scale 0.98
- The compose FAB casts the only meaningful shadow in the app: `rgba(224,86,111,0.35) 0 6px 16px`

**Secondary / Outline Button (Export · Share)**
- Background: transparent
- Text/Icon: `#FF8A65` (Bear Orange)
- Border: 1pt `#FF8A65`
- Padding: 11pt vertical, 20pt horizontal
- Corner radius: 12pt
- Font: Inter 15pt weight 500
- Pressed: background `#FF8A6514` (8% orange)

**Tag Pill Button (Add Tag)**
- Background: `#2F343D` dark / `#F6F6F4` light
- Text: `#E0566F` (Bear Red)
- Border: 1pt `#353A42` / `#E6E6E2`
- Corner radius: 999pt (pill)
- Padding: 8pt vertical, 14pt horizontal
- Font: Inter 14pt weight 500

**Text Button (Show all notes / Cancel)**
- Font: Inter 15pt weight 500
- Color: `#FF8A65`
- No underline, no background
- Pressed: opacity 0.6

**Icon Button (nav + info-bar glyphs)**
- 20pt glyph in a 44pt hit area
- Color: `#FF8A65` (Bear Orange) — every interactive glyph in the app is orange
- Pressed: opacity 0.5

### Core Atoms

**#hashtag Token (inline + sidebar)**
- Inline in prose: text-only, no background, color `#E0566F`, Inter 14pt w500 — flows with the line
- Sidebar tag row: 36pt tall, leading 16pt tag glyph, label Inter 14pt w500, nested children indent 18pt with a disclosure chevron
- Active tag row: `#2F343D` background, label brightens
- Tag chip variant (filter bar): pill, 6pt/12pt padding, tinted background at ~14% of the tag color

**Markdown Checkbox (`- [ ]` / `- [x]`)**
- Box: 19pt, 5pt corner radius
- Unchecked: 2pt border `#6B7280`, transparent fill
- Checked: gradient `#E0566F → #FF8A65` fill, white 3.4-weight checkmark, no border
- Checked label: `#6B7280` with strike-through
- Tap toggles in place with a 150ms ease + soft haptic

**Quote Block (`> `)**
- Left rule: 3pt solid `#FF8A65` (Bear Orange)
- Padding: 2pt vertical, 14pt left
- Font: Lora 17pt w400 *italic*, color `#9DA3AD` (secondary)
- No background, no right border

**Inline Code (`` `code` ``)**
- Background: `#282C34` dark / `#F0F0EC` light
- Padding: 2pt vertical, 5pt horizontal
- Corner radius: 5pt
- Font: JetBrains Mono 14pt w400, color `#E8EAED`

**Code Block (fenced ```)**
- Background: `#282C34` dark / `#F6F6F4` light
- Padding: 14pt all
- Corner radius: 8pt
- Font: JetBrains Mono 13pt, line height 1.55
- Optional language label top-right `#6B7280` 12pt
- Lightweight syntax tint: keywords `#FF8A65`, strings `#57C98B`

**Link (`[text](url)` / `[[wiki]]`)**
- Color: `#5AAFEF`, underlined 1pt
- Font: inherits body (Inter 16pt w400)
- `[[wiki-links]]` get the same blue, no brackets shown

### Navigation

**Editor Top Nav**
- Height: 44pt + safe area
- Leading: back-chevron + "Notes" label, Inter 15pt w500, `#FF8A65`
- Trailing: 2–3 glyphs (info / format / more), 19pt, `#FF8A65`, 20pt gap
- No title in the bar — the note's own `#` title is the title; nav is transparent over canvas

**Tag Sidebar (left pane / pushed screen)**
- Width: 280pt (full screen on iPhone as a pushed screen)
- Background: `#282C34` dark / `#F6F6F4` light
- Top: smart filters — Notes, Untagged, Todo, Today, Locked, Archive, Trash — each a 40pt row with leading glyph
- Below: the #hashtag tree; each tag row 36pt, nested indent 18pt with disclosure chevron
- Active row: `#2F343D` background; tag glyphs tinted `#E0566F`

**Note List (middle pane)**
- Each row: ~84pt tall, 16pt insets
- Title: Inter 17pt w600 `#E8EAED`
- Preview: Inter 14pt w400 `#9DA3AD`, 2 lines, truncated
- Trailing: relative date `#6B7280` 13pt
- Selected row: `#2F343D` background, 3pt leading accent rule in tag color
- Section headers (when grouped by tag): the tag name in its accent color, Inter 13pt w600 uppercase

**Bottom Info Bar (editor)**
- Height: 60pt + safe area
- Background: `#282C34` at 85% + blur, 0.5pt top border `#353A42`
- Leading: live "248 words · 1,492 chars" Inter 12pt `#6B7280`
- Trailing: action glyphs (table, preview, export, info) 20pt `#FF8A65`, 22pt gap

### Input Fields

**Search Bar (note list / sidebar)**
- Height: 40pt
- Background: `#2F343D` dark / `#ECECE8` light
- Corner radius: 10pt
- Leading magnifier 16pt `#6B7280`
- Placeholder "Search notes" Inter 14pt `#6B7280`
- Focus: 1pt `#FF8A65` border

**Title / Body Editor Field**
- No box, no border, no background — pure text on canvas
- Title: Inter 24pt w700; placeholder "Title" `#6B7280`
- Body: Inter 16pt w400, 22pt side insets, 1.55 line height
- Markdown renders live as typed; the keyboard accessory row holds formatting shortcuts (#, **, `- [ ]`, `>`, ```, link)

### Distinctive Components

**Live Markdown Renderer**
- The signature behavior: as the user types Markdown, syntax markers fade to `#6B7280` and styling applies in-place — no preview toggle. `# ` → 24pt bold; `**x**` → bold with dimmed asterisks; `- [ ]` → tappable checkbox; `> ` → orange-ruled italic; ``` → mono code block.

**#hashtag Organization**
- Typing `#tag` mid-prose creates a live, tappable red token. `#parent/child` auto-creates a nested sidebar branch. There are *no folders* — the tag tree is the whole filing system. Tapping a tag filters the note list to that tag.

**Compose FAB**
- 56pt circle, gradient `#E0566F → #FF8A65`, white `+` glyph, bottom-trailing, shadow `rgba(224,86,111,0.35) 0 6px 16px` — the one elevated element in the app.

**Theme Engine**
- Bear ships multiple full themes (Charcoal, Red Graphite, Dark Graphite, Solarized, Dracula…). Each theme retripes canvas + surfaces + syntax tints; the red→orange brand gradient persists across all of them as the constant identity.

**Tag-Colored Section Headers**
- When the note list is grouped, each group header is the tag name rendered in that tag's accent color (default `#E0566F`) — the only place color enters the list.

**Export Sheet**
- Bottom sheet, 12pt top corners, lists export formats (PDF, Markdown, HTML, DOCX, JPG, TXT). Each row 52pt with leading format glyph; primary "Export" button uses the brand gradient.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 22, 24, 32, 40, 48, 64
- Editor side inset: 22pt (wider than typical UI — reading measure)
- Paragraph vertical gap: 16pt; before headings: 20pt
- Note-list row inset: 16pt; row height ~84pt

### Grid & Container
- iPhone: single pushed pane at a time (sidebar → list → editor), full width, 22pt editor inset
- iPad portrait: 2-pane (list 320pt + editor)
- iPad landscape: 3-pane (sidebar 280pt + list 320pt + editor flexible, max measure ~720pt)
- Editor text column caps reading measure on wide screens; never full-bleed text on iPad

### Whitespace Philosophy
- **Prose breathes**: 1.55 body line height, 22pt side margins, 16pt paragraph gaps — tuned for reading sessions, not screen density
- **Chrome recedes**: transparent nav, no card borders around content, a single elevated FAB
- **One accent**: color appears only on hashtags, the FAB, checkboxes and selection — everything else is greyscale type
- **Code stands apart**: code blocks get a filled surface + 14pt padding so they read as a distinct zone without borders

### Border Radius Scale
- Square (0pt): dividers, full-bleed nothing (Bear has no hero images)
- Subtle (5pt): inline code chip, checkbox
- Standard (8pt): code blocks, search bar (≈10pt)
- Comfortable (12pt): buttons, export sheet rows
- Large (22pt): bottom-sheet top corners
- Pill (500pt): tag-pill buttons, filter chips
- Circle (50%): compose FAB, avatars

## 6. Depth & Elevation

Bear is almost entirely flat — depth is reserved for true overlays and the one FAB.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Editor canvas, prose blocks, sidebar/list rows, code blocks |
| Accent FAB | `rgba(224,86,111,0.35) 0 6px 16px` | Compose-note floating button only |
| Floating (Level 1) | `rgba(0,0,0,0.18) 0 4px 16px` | Format popover, tag autocomplete, more-menu |
| Sheet (Level 2) | `rgba(0,0,0,0.28) 0 -6px 28px` | Export sheet, theme picker, share sheet |
| Modal Overlay | `rgba(0,0,0,0.45)` | Dim behind sheets and the lock screen |

**Shadow Philosophy**: shadows are a "this floats, tap outside to dismiss" signal, not a style. The compose FAB is the single exception — it gets a tinted red glow to read as the primary action. On dark themes shadows nearly vanish, so floating panels add a 1pt `#353A42` border as the secondary elevation cue.

### Motion
- **Markdown render**: styling applies instantly on keystroke; the syntax-marker fade is a 120ms opacity transition (marker → `#6B7280`), never a layout jump
- **Checkbox toggle**: fill cross-fades to gradient over 150ms ease + soft haptic; label strike-through draws left→right in 150ms
- **Tag tap → filter**: note list cross-fades (200ms) to the filtered set; pushed screens use the iOS native 300ms horizontal slide
- **Sidebar / list / editor**: standard iOS push (300ms) on iPhone; on iPad pane changes cross-fade content (200ms), panes don't move
- **Compose FAB**: tap scales 0.92 then opens the new note with a 280ms ease-out push; FAB fades out during transition
- **Export / theme sheet**: slides up 300ms ease-out; dim fades in parallel
- **Disclosure chevron**: rotates 90° in 150ms when expanding a nested tag branch; children slide down height 0→auto in 200ms ease-out
- **Haptic feedback**: soft impact on checkbox toggle, tag tap, FAB press, theme switch; success haptic on export complete

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) light / warm charcoal (`#21252B`) dark — Bear's canvas is calm, never pure black
- Render Markdown *live and in-place* — dim the syntax marker to `#6B7280`, never hide structure or show a preview pane
- Make #hashtags inline red (`#E0566F`) tappable tokens — they are the entire organization model; there are no folders
- Keep the red→orange gradient (`#E0566F → #FF8A65`) as the *only* accent — icon, FAB, checkbox, selection
- Tint every interactive glyph Bear Orange (`#FF8A65`) — nav, info-bar, list affordances
- Give prose room: 22pt side insets, 1.55 line height, 16pt paragraph gaps
- Style quotes with a 3pt orange left rule + Lora italic — no background
- Respect the user's chosen type family (Sans/Serif/Mono) globally; only code is always JetBrains Mono
- Use the bottom info bar for live word/char count + export/link/pin actions
- Keep the compose FAB as the single elevated element, with its tinted red glow

### Don't
- Don't add a separate Markdown preview screen — live in-place rendering is the signature
- Don't introduce folders or a second accent color — tags + the single gradient are the brand
- Don't box prose in cards, borders or shadows — the editor is one uninterrupted column
- Don't use pure black (`#000000`) for text or canvas — warm `#2A2C33` text / `#21252B` canvas
- Don't tint nav/info glyphs anything but Bear Orange — color discipline is the identity
- Don't hard-code Inter — users can switch to serif (Lora) or mono (JetBrains Mono)
- Don't render hashtags as plain text or as chips inside prose — inline red token, no background, flows with the line
- Don't animate aggressively — motion is quiet (120–300ms ease-out); the marker fade never causes a layout jump
- Don't put shadows on content blocks — only the FAB and true overlays get elevation
- Don't crowd the line — Bear's wide margins and tall leading are deliberate reading ergonomics
- Don't bold body prose by default — body is 400; bold is bold

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single pushed pane; editor inset 18pt; FAB 52pt |
| iPhone 13/14/15 | 390pt | Standard single-pane push; editor inset 22pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by transparent top nav |
| iPhone 15/16 Pro Max | 430pt | Editor inset 24pt; slightly larger reading measure |
| iPad (portrait) | 768pt | 2-pane: note list 320pt + editor (measure-capped) |
| iPad (landscape) | 1024pt+ | 3-pane: tag sidebar 280pt + list 320pt + editor (max measure ~720pt) |

### Dynamic Type
- Note title, headings, body, quote, preview, meta: full scale
- Tag tokens, info-bar caption (12pt), sidebar filter labels, tab labels: FIXED (layout-sensitive)
- Code blocks: scale but stay JetBrains Mono

### Orientation
- All screens rotate
- iPad gains a pane on landscape (2-pane → 3-pane)
- Editor reading measure re-caps on width change; text reflows, side insets hold

### Touch Targets
- Nav / info-bar glyph: 20pt glyph, 44pt hit
- Checkbox: 19pt box, 44pt hit row
- Sidebar tag row: 36pt tall, full-row tap
- Note-list row: ~84pt, full-row tap
- Compose FAB: 56pt (52pt on SE)
- Disclosure chevron: 16pt glyph, 44pt hit

### Safe Area Handling
- Top: transparent nav respects safe area + Dynamic Island
- Bottom: info bar + home indicator respected; FAB sits above the home indicator
- Keyboard: editor scrolls above keyboard; the formatting accessory row docks to the keyboard top
- Sides: 22pt editor inset (18pt on SE); list rows 16pt

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark / Charcoal): `#21252B`
- Surface 1 (dark): `#282C34`
- Divider (dark): `#353A42`
- Text primary: `#2A2C33` light / `#E8EAED` dark
- Text secondary / dimmed markers: `#6B7280` / `#9DA3AD`
- Bear Red (hashtags): `#E0566F`
- Bear Orange (glyphs / quote rule): `#FF8A65`
- Brand gradient: `#E0566F → #FF8A65` (135°)
- Link blue: `#5AAFEF`
- Tag green: `#57C98B` · Tag blue: `#5AAFEF` · Tag yellow: `#F2C14E`
- Error red: `#F2545B`
- Code block bg: `#282C34` (dark) / `#F6F6F4` (light)
- Selection tint: `#E0566F26`

### Example Component Prompts
- "Build the Bear note editor in SwiftUI: transparent top nav with a back-chevron + 'Notes' label in Bear Orange `#FF8A65` 15pt w500, plus 2 trailing 19pt orange glyphs. Canvas `#21252B`. Body is a single text column at 22pt side insets: the first line is the note title in Inter 24pt w700 `#E8EAED`; below, prose in Inter 16pt w400 line-height 1.55. Render Markdown live — `# ` makes a 24pt bold heading and dims the `#` to `#6B7280`; `- [ ]` becomes a 19pt 5pt-radius checkbox. Bottom info bar `#282C34` @85% blur, 0.5pt `#353A42` top border, leading 'N words · M chars' in Inter 12pt `#6B7280`, trailing 4 orange 20pt action glyphs."

- "Create a Bear #hashtag token: inline in the text run, no background, color `#E0566F`, Inter 14pt w500, tappable, flows with the line. Nested form `#parent/child` registers a sidebar branch. On tap, filter the note list to that tag with a 200ms cross-fade."

- "Render a Bear Markdown checkbox row: 19pt box with 5pt corner radius. Unchecked = 2pt border `#6B7280`, transparent fill. Checked = linear-gradient `#E0566F → #FF8A65` fill with a white 3.4-weight checkmark, no border, and the label in `#6B7280` with strike-through. Tap toggles in place over 150ms ease with a soft haptic; the strike-through draws left→right in 150ms."

- "Build the Bear tag sidebar: 280pt wide, `#282C34` background. Top section = smart filters (Notes, Untagged, Todo, Today, Archive, Trash) as 40pt rows with leading 18pt glyphs. Below = the #hashtag tree; each tag row 36pt with a leading 16pt tag glyph tinted `#E0566F`, label Inter 14pt w500, nested children indented 18pt behind a disclosure chevron that rotates 90° over 150ms. Active row: `#2F343D` background."

- "Create the Bear compose FAB: 56pt circle, linear-gradient `#E0566F → #FF8A65` at 135°, white `+` glyph, bottom-trailing with 16pt margins, shadow `rgba(224,86,111,0.35) 0 6px 16px`. Tap scales to 0.92 then opens a new blank note with a 280ms ease-out push."

- "Build a Bear quote block: a 3pt solid `#FF8A65` left rule, 14pt left padding, no background, text in Lora 17pt w400 italic `#9DA3AD`. Place a fenced code block below it: `#282C34` background, 8pt corner radius, 14pt padding, JetBrains Mono 13pt line-height 1.55, keywords tinted `#FF8A65` and strings `#57C98B`, optional language label top-right in `#6B7280` 12pt."

### Iteration Guide
1. Canvas is white (`#FFFFFF`) light / warm charcoal `#21252B` dark — NOT pure black, NOT off-white
2. Text primary is warm `#2A2C33` / `#E8EAED` — body at 400, headings at 700; reading line height 1.55
3. The red→orange gradient `#E0566F → #FF8A65` is the *only* accent — icon, FAB, checkbox, selection; everything else greyscale
4. Every interactive glyph is Bear Orange `#FF8A65` — nav, info-bar, affordances; never another tint
5. Markdown renders live and in-place — dim markers to `#6B7280`, no preview pane ever
6. #hashtags are inline red (`#E0566F`) tappable tokens and the *entire* filing system — no folders
7. Prose breathes: 22pt side insets, 1.55 line height, 16pt paragraph gaps — reading ergonomics, not density
8. Quotes = 3pt orange left rule + Lora italic; code = filled `#282C34` surface + JetBrains Mono, no borders
9. Typography is user-switchable (Inter / Lora / JetBrains Mono) globally — only code is always mono
10. One elevated element: the compose FAB with its tinted red glow; content blocks are flat, motion is quiet (120–300ms)
