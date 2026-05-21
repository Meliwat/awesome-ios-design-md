# Design System Inspiration of Notion (iOS)

## 1. Visual Theme & Atmosphere

Notion's iOS app is an almost-invisible design system. The canvas is pure white (`#FFFFFF`) with barely any chrome, because Notion's philosophy is that the *content you write* is the interface. Open a page and you'll see a hero cover image (full-bleed, 120-200pt tall), a large emoji sitting just above the title, the title itself in Inter 32pt bold, and then a flow of "blocks" — paragraphs, headings, toggles, callouts, images — with no obvious framing, borders, or cards. This radical minimalism is deliberate: Notion is a blank canvas that takes on the shape of whatever the user is building (a journal, a wiki, a project tracker, a meeting note) and the UI is engineered to recede until it looks like a well-typed document.

The one piece of chrome that does appear is the **block handle** — a tiny 8pt dot-grid icon (⋮⋮⋮) and a `+` button that materialize on the left edge of a block when you hover (desktop) or long-press (mobile). These handles are the "interface" of Notion; they let you drag-reorder, duplicate, and insert blocks. The other signature UI moment is the **`/` command palette** — typing `/` at the start of a line opens a floating menu of block types (Heading, Toggle, Image, Callout, Database, Code, etc.) that is the primary creation gesture. Everything built in Notion flows through these two affordances.

The color system is muted, near-greyscale pastels. Page backgrounds can be set to nine colors (default white, gray, brown, orange, yellow, green, blue, purple, pink, red) — all desaturated and nearly paper-like (`#F1F1EF` gray, `#FAEBDD` orange, `#FBF3DB` yellow, `#EDF3EC` green, `#E7F3F8` blue, `#F6F3F9` purple, `#FAF1F5` pink, `#FDEBEC` red). These same hues (at stronger saturation) appear as callout block backgrounds, highlight chips on inline text, database tag colors, and cover images. The app has no primary CTA color — action is signaled by typography weight and position, not by a "Spotify Green" or "Gmail Red". The closest thing to an accent is links (`#2E75CC`) and the subtle Notion Black (`#000000`) on the primary "New page" button.

Typography is user-switchable in settings: **Inter** (default sans, the modern workhorse), **Lora** (optional serif, for more literary notes), or **IBM Plex Mono** (optional mono, for code/technical users). On iOS it falls back to SF Pro when the user's chosen face isn't bundled. Hierarchy is document-like: title at 32pt, heading 1 at 24pt, heading 2 at 20pt, heading 3 at 16pt, body at 16pt. All headings are weight 700 bold; body at 400. The type system prioritizes reading rhythm over brand expression — it reads like a well-designed Medium article, which is exactly what Notion wants.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / dark charcoal (`#191919`) dark — minimum chrome, maximum content
- Block-based editor — every paragraph, heading, toggle, image is a draggable/replaceable "block"
- Block handles (⋮⋮⋮ + `+`) appear on hover/long-press as the primary interface affordance
- `/` command palette — type `/` to insert any block type — the core creation gesture
- Emoji as page icon + full-bleed cover image — the page "hero" is user-chosen, not designer-set
- Nine muted pastel page backgrounds + callout/highlight colors (Gray / Brown / Orange / Yellow / Green / Blue / Purple / Pink / Red)
- Inter / Lora / IBM Plex Mono — user-switchable typography in settings
- Toggle blocks — collapsible sections with ▸/▾ triangle affordance
- Database views: Table, Board (Kanban), Timeline, Calendar, Gallery, List — each a different rendering of the same block-row data
- Mentions (@-person or @-page) as inline interactive chips with avatar
- Minimal navigation chrome: slide-in sidebar from left, bottom tab strip with Home / Search / Updates / Settings

## 2. Color Palette & Roles

### Primary (Interactive)
- **Notion Black** (`#000000`): Primary interactive color — "New page" button background, primary CTAs.
- **Notion Black Pressed** (`#1A1A1A`): Pressed state.
- **Link Blue** (`#2E75CC`): Hyperlinks, @-mentions, selected text.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F7F6F3`): Sidebar background, subtle elevated surfaces (e.g., "Recently visited" chips), hovered rows.
- **Surface Pressed** (`#EFEEE9`): Pressed/selected rows.
- **Divider** (`#EAECEC`): Hairline dividers between database rows and between page sections.
- **Selection Highlight** (`#2E75CC33`): 20% blue overlay on selected text.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#191919`): Primary dark canvas — Notion's signature dark, NOT pure black.
- **Dark Surface 1** (`#202020`): Sidebar, elevated surfaces.
- **Dark Surface 2** (`#2F2F2F`): Hovered rows, card backgrounds.
- **Dark Divider** (`#373737`): Hairline dividers.
- **Dark Link Blue** (`#529CCA`): Link color shifts lighter for contrast.

### Text
- **Text Primary Light** (`#37352F`): Primary body text — a warm dark gray, NOT pure black.
- **Text Secondary Light** (`#9B9A97`): Placeholder, metadata, block handle icons.
- **Text Tertiary Light** (`#C3C2BF`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#E6E6E4`): Primary body on dark.
- **Text Secondary Dark** (`#9B9A97`): Same secondary on both modes (works for both).

### Page Background Colors (9 Muted Pastels, Light)
These are the page-background options available to users — also used as callout/highlight backgrounds and database tag chip fills.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Default | `#FFFFFF` | `#191919` |
| Gray | `#F1F1EF` | `#2F2F2F` |
| Brown | `#F4EEEE` | `#4A3228` |
| Orange | `#FAEBDD` | `#5C3B23` |
| Yellow | `#FBF3DB` | `#564328` |
| Green | `#EDF3EC` | `#243D30` |
| Blue | `#E7F3F8` | `#143A4E` |
| Purple | `#F6F3F9` | `#3C2B4A` |
| Pink | `#FAF1F5` | `#4B2740` |
| Red | `#FDEBEC` | `#512E29` |

### Callout / Highlight Text Colors (9 Saturated Pastels)
For inline text highlights and callout text.

| Role | Color |
|------|-------|
| Gray Text | `#9B9A97` |
| Brown Text | `#64473A` |
| Orange Text | `#D9730D` |
| Yellow Text | `#DFAB01` |
| Green Text | `#0F7B6C` |
| Blue Text | `#0B6E99` |
| Purple Text | `#6940A5` |
| Pink Text | `#AD1A72` |
| Red Text | `#E03E3E` |

### Semantic
- **Error Red** (`#E03E3E`): Inline error toast, delete confirmation.
- **Success Green** (`#0F7B6C`): Save-success toast.
- **Warning Orange** (`#D9730D`): Warning callouts.
- **Mention Background** (`#2E75CC1A`): 10% blue behind @-mentions inline.

## 3. Typography Rules

### Font Family
- **Default**: `Inter` (by Rasmus Andersson, 2020, SIL OFL licensed) — geometric humanist sans optimized for UI at small sizes, excellent hinting, extensive weight range.
- **Serif Option**: `Lora` (Google Fonts, SIL OFL) — transitional serif with calligraphic features, used for literary notes.
- **Mono Option**: `IBM Plex Mono` (by Bold Monday for IBM, SIL OFL) — technical mono face used for code blocks and tech-heavy note-takers.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **User-Switchable in Settings**: Default (Inter), Serif (Lora), Mono (IBM Plex Mono) — per user setting, applied to all pages

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Page Title | Inter | 32pt | 700 | 1.2 | -0.5pt | Below page icon & cover, bold hero |
| Heading 1 (`/h1`) | Inter | 24pt | 700 | 1.25 | -0.3pt | Top-level section heading in page |
| Heading 2 (`/h2`) | Inter | 20pt | 700 | 1.3 | -0.2pt | Sub-section |
| Heading 3 (`/h3`) | Inter | 16pt | 700 | 1.4 | -0.1pt | Sub-sub-section |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Paragraph block default |
| Body (Dense) | Inter | 15pt | 400 | 1.4 | 0pt | Compact mode (user setting) |
| Code Inline | IBM Plex Mono | 14pt | 400 | 1.4 | 0pt | `inline code` highlighted with `#F7F6F3` bg |
| Code Block | IBM Plex Mono | 13pt | 400 | 1.5 | 0pt | Code blocks in fenced block |
| Caption | Inter | 14pt | 400 | 1.4 | 0pt | Image captions, small text |
| Quote | Lora | 17pt | 400 | 1.5 | 0pt | Italic quote block |
| Button | Inter | 14pt | 600 | 1.0 | 0pt | Primary/secondary buttons |
| Link | Inter | 16pt | 400 | 1.5 | 0pt | Inline link — underlined `#2E75CC` |
| Mention | Inter | 16pt | 500 | 1.5 | 0pt | @-chip text |
| Block Handle | Inter | 10pt | 400 | 1.0 | 0pt | ⋮⋮⋮ and + icons (actually icons, but adjacent caption uses this) |
| Placeholder | Inter | 16pt | 400 | 1.5 | 0pt | "Type '/' for commands" |
| Tab Label | Inter | 11pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Sidebar Label | Inter | 14pt | 500 | 1.3 | 0pt | Sidebar page names |

### Principles
- **Document rhythm, not UI rhythm**: Type sizes and weights follow editorial conventions (title > H1 > H2 > H3 > body), not product-UI conventions (hero > section > card > caption).
- **Weight concentrated at 400 / 500 / 600 / 700**: Regular body, medium for emphasis, semibold for buttons, bold for headings. No thin, no black.
- **User controls family**: Respect the user's chosen type family (Inter/Lora/Mono) globally — don't hardcode Inter.
- **Monospace is for code only**: IBM Plex Mono appears on inline code and code blocks; nothing else.
- **Dynamic Type first-class**: Titles, headings, body, captions all scale; block handles, tab labels, mention chips stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (New Page / Share / Save)**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#000000` (Notion Black) / `#E6E6E4` (dark mode — inverted)
- Text: `#FFFFFF` light / `#191919` dark
- Padding: 8pt vertical, 14pt horizontal
- Font: Inter 14pt weight 600
- Pressed: background `#1A1A1A` light / `#D1D0CB` dark + scale 0.98
- Leading icon (optional): SF Symbol 14pt

**Secondary / Ghost Button**
- Background: transparent
- Text: `#37352F` light / `#E6E6E4` dark
- Border: 1pt `#EAECEC`
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 6pt
- Font: Inter 14pt weight 500
- Pressed: background `#F7F6F3`

**Icon Button (Block Handle `⋮⋮⋮` / `+`)**
- Size: 20pt glyph in a 28pt hit area
- Default color: `#9B9A97` (Text Secondary) at 50% opacity — appears subtle
- Hover / active: 100% opacity
- The "⋮⋮⋮" (dot-grid) opens the block menu; the "+" inserts a new block below
- Spacing from block content: 4pt

**Text Button (Link in sidebar / action)**
- Font: Inter 14pt weight 500
- Color: `#37352F`
- No underline
- Pressed: background `#F7F6F3` (full row highlight in sidebar)

### Blocks (the core atom)

Every content unit in Notion is a "block". Blocks have:
- Hover / long-press handle: ⋮⋮⋮ (20pt, left of block at 4pt gap)
- Insert-below affordance: `+` (same position)
- Content area: full-width (minus 16pt page insets)
- 6-8pt vertical gap between blocks

**Paragraph Block**
- Content: body text 16pt w400 `#37352F`
- Empty state placeholder: "Type '/' for commands" in `#9B9A97`

**Heading 1 / 2 / 3 Block**
- Content: 24 / 20 / 16pt w700
- 16pt top margin, 6pt bottom margin

**Toggle Block** (collapsible)
- Leading chevron: ▸ (closed) / ▾ (open), 16pt `#9B9A97`
- Content: inline heading text, 16pt w500
- Expanded: children blocks indented 24pt below

**Callout Block**
- Background: user-picked page-bg color (e.g., `#FBF3DB` yellow)
- Border: none
- Padding: 16pt all
- Corner radius: 4pt
- Leading emoji: 20pt glyph
- Content: body text 16pt w400

**Code Block**
- Background: `#F7F6F3` light / `#2F2F2F` dark
- Padding: 16pt
- Corner radius: 4pt
- Font: IBM Plex Mono 13pt
- Language label: top-right corner, `#9B9A97` 12pt
- "Copy" button: top-right, appears on hover

**Quote Block**
- Left border: 3pt solid `#37352F`
- Padding: 4pt top/bottom, 14pt left
- Font: Inter 16pt w400 (or Lora 17pt if user has serif preference)
- Italic by default

**Divider Block**
- A single 1pt `#EAECEC` horizontal line with 16pt vertical margin

**Image / Video Block**
- Full-width minus 16pt insets
- Corner radius: 4pt
- Optional caption below: 14pt w400 `#9B9A97` italic

**Page Link Block (inline)**
- Leading page-icon emoji (16pt)
- Text: page title, underlined 1pt
- Font: Inter 16pt w500

**Database Block (Table / Board / Calendar / Gallery / Timeline / List)**
- A full micro-app inside a block — renders its rows in one of 6 view modes
- Views selectable via tabs at top of database
- Add-row affordance: `+ New` at bottom of table view

### Page Structure

**Page Header**
- Full-bleed cover image (if set), 120pt tall on mobile, 200pt on tablet
- Page emoji icon: 64pt glyph, positioned with -32pt top offset overlapping cover if present
- Page title: Inter 32pt w700 `#37352F`, 16pt horizontal inset, 16pt top margin from icon
- Sub-actions row below title: `Add icon`, `Add cover`, `Add comment`, `Add link` — all ghost-gray small buttons

**Page Body**
- Full-width minus 16pt insets
- Flow of blocks
- Empty state: "Type '/' for commands" placeholder in first block

### Navigation

**Left Sidebar (slides in from left)**
- Width: 280pt (or 80% of screen width on small devices)
- Background: `#F7F6F3` light / `#202020` dark
- Slides in with 250ms ease-out when user swipes right from left edge OR taps hamburger
- Sections:
  1. Workspace header: workspace icon (24pt) + name + expand chevron
  2. "Teamspaces" section
  3. "Shared" section
  4. "Private" section
  5. "Settings & members" link at bottom
- Each page row: 32pt tall, leading 16pt icon (emoji or default page icon), page title 14pt w500, trailing ⋯ menu (visible on row hover)
- Active page: `#EFEEE9` row background
- Nested pages: 24pt indent per level, leading chevron ▸/▾

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#191919` with 0.5pt top border `#EAECEC` / `#373737`
- Tabs (4): Home, Search, Updates, Settings (or sometimes "Jump to", "AI", "New page")
- Icon size: 24pt
- Active color: `#37352F` / `#E6E6E4` (no tint pill — just icon fill change)
- Inactive: `#9B9A97`
- Labels: Inter 11pt w500, shown always

**Top Nav (Page view)**
- Height: 44pt + safe area
- Leading: hamburger (opens sidebar) or back chevron
- Center: page title (compact, shown when scrolled past hero title)
- Trailing: share icon + ⋯ ellipsis (page actions menu)

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#F7F6F3` / `#2F2F2F` dark
- Border: none
- Corner radius: 6pt
- Leading icon `magnifyingglass` 16pt `#9B9A97`
- Placeholder: "Search" 14pt w400 `#9B9A97`
- Focus: 1pt `#2E75CC` border

**`/` Command Palette (appears when you type `/`)**
- A floating panel at cursor position
- Width: 280pt (or full screen width on small devices with 16pt insets)
- Background: `#FFFFFF` with shadow `rgba(0,0,0,0.08) 0 4px 12px`
- Border: 1pt `#EAECEC`
- Corner radius: 6pt
- Max height: 360pt with scroll
- Each option row:
  - Height: 40pt
  - Leading: block-type icon 20pt (text, heading, toggle, image, callout, code, etc.)
  - Title: Inter 14pt w500 `#37352F`
  - Subtitle: Inter 12pt w400 `#9B9A97` (block description)
  - Trailing keyboard-shortcut hint (desktop only): `⌘` glyph + letter in 12pt monospace
- Selected row: `#EFEEE9` background
- Keyboard navigation with up/down arrows; tap to insert

**@-Mention Picker (appears when you type `@`)**
- Similar floating panel
- Shows people, pages, dates as selectable rows
- Person row: 24pt avatar + name
- Page row: page emoji + page title
- Date row: calendar icon + date text

### Distinctive Components

**Block Handle (⋮⋮⋮) + Insert (`+`)**
- On mobile, long-press a block for 400ms surfaces the handles
- On desktop, hover shows them
- Drag ⋮⋮⋮ to reorder blocks; tap ⋮⋮⋮ to open block menu (Turn into, Copy link, Duplicate, Move to, Delete)
- Tap `+` to open block-type picker inline below current block

**`/` Command Palette**
- Cursor-anchored floating panel — see Input Fields above
- The heart of Notion's creation UX

**Toggle Block**
- Click ▸ arrow to expand children (slide down animation, 200ms ease-out)
- Nested content indented 24pt

**Callout with Emoji**
- Pastel-colored block with leading emoji — "⚠️ Warning: ..." / "💡 Tip: ..."
- User picks emoji and background color

**Database Views (switcher tabs at top of database block)**
- Row of view tabs: "Table", "Board", "Timeline", "Calendar", "Gallery", "List"
- Active tab: underlined 2pt `#37352F`
- Inactive: `#9B9A97`
- Below: the rendered view (Table = grid, Board = Kanban columns, etc.)

**Mention Chip (inline @-person or @-page)**
- Inline element in text flow
- Leading avatar / page-icon (16pt)
- Text with 10% blue background (`#2E75CC1A`), `#2E75CC` color, Inter 16pt w500
- 2pt horizontal padding, 2pt corner radius

**Cover Image Hero**
- Full-bleed image at top of page
- Hover reveals "Change cover" and "Reposition" small buttons in top-right
- User can pick from Unsplash gallery, upload, or solid color

**Page Icon (Emoji)**
- 64pt emoji sitting in the page header, overlapping the cover image if present
- Tap to open emoji picker
- Default icon: blank page outline in `#9B9A97`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 96
- Block vertical gap: 6pt (tight reading flow)
- Heading top margin: 16pt (extra breathing room)
- Page content inset: 16pt horizontal

### Grid & Container
- iPhone: content at 16pt horizontal insets, full-bleed cover
- iPad: content at max-width 760pt centered, 24pt insets
- Sidebar width: 280pt (collapsed to 0 on iPhone; always visible on iPad landscape)
- Database table: horizontal scroll within the block

### Whitespace Philosophy
- **Document rhythm**: 6pt between paragraph blocks, 16pt before headings — reads like a well-designed article
- **Minimal chrome everywhere**: the only chrome is block handles (which appear on hover / long-press, not always visible)
- **Hero zone is optional**: cover image + emoji + title zone can be any height user wants (no cover = 0pt hero)
- **Callouts and code breathe**: 16pt internal padding, so they stand out from inline text without needing borders

### Border Radius Scale
- Square (0pt): Divider lines, full-bleed covers
- Subtle (4pt): Callout blocks, code blocks, image blocks, chips
- Standard (6pt): Buttons, search bar, / command palette
- Comfortable (12pt): Bottom sheets, modals
- Pill (500pt): Rarely used — the toggle switches in settings
- Circle (50%): Avatars

## 6. Depth & Elevation

Notion is radically flat. Shadows exist only in three contexts:

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Page content, blocks, sidebar rows |
| Floating (Level 1) | `rgba(0,0,0,0.08) 0 4px 12px` | `/` command palette, @-mention picker, block menu |
| Sheet (Level 2) | `rgba(0,0,0,0.1) 0 -4px 24px` | Bottom sheets (Share, emoji picker, move-to-workspace) |
| Modal Overlay | `rgba(0,0,0,0.3)` | Dim behind modals and bottom sheets |

**Shadow Philosophy**: Notion doesn't use shadow as a visual style — it uses it purely as a "this is floating, tap outside to dismiss" signal. The command palette, mention picker, and block menu all get a light `0 4px 12px` shadow so they clearly float above the page without adding visual noise. On dark mode, shadows become harder to see, so Notion adds a subtle 1pt border to floating panels as a secondary affordance.

### Motion
- **Block reorder (drag)**: 60fps drag following finger; other blocks shift over 200ms ease-out to make room; drop snaps into position with 150ms ease-out + soft haptic
- **Toggle expand/collapse**: chevron rotates 90° in 150ms; children slide down with height 0 → auto over 200ms ease-out
- **`/` command palette**: fade-in + 4pt slide-down in 150ms ease-out; fade-out + 4pt slide-up on dismiss; arrow-key selection is instant (no animation)
- **Page navigation**: push transition in 300ms (iOS native push), horizontal slide
- **Sidebar swipe**: edge-swipe drags sidebar with 1:1 finger tracking; release threshold at 50% commits open/close with 250ms spring damping 0.8
- **Block handle reveal**: on long-press (400ms hold), handles fade-in over 120ms
- **Selection highlight**: blue `#2E75CC33` overlay appears instantly on text selection
- **Emoji picker**: bottom sheet slides up with 300ms ease-out
- **Page save (auto)**: no visible motion — silent; toast "Saved" appears only on manual save or error
- **Haptic feedback**: soft impact on toggle expand, block drag start, command palette open

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas — Notion is paper
- Use dark charcoal (`#191919`) for dark mode — NOT true black
- Use warm dark text (`#37352F`) — NOT pure black
- Surface block handles (⋮⋮⋮ + `+`) on long-press (mobile) or hover (desktop) — never always-visible
- Use the `/` command as the primary block-creation gesture — it's the heart of Notion
- Respect user's chosen font family globally (Inter / Lora / IBM Plex Mono)
- Render callouts with pastel backgrounds + leading emoji — never with borders
- Use toggles (▸/▾) for collapsible sections — the chevron always leading, 4pt from text
- Embed databases as blocks — they have their own view switcher (Table / Board / Timeline / Calendar / Gallery / List)
- Make mentions inline chips with avatar + blue background — never plain blue text
- Keep chrome minimal — content is the interface

### Don't
- Don't use card-style borders around content blocks — Notion is flat, content flows inline
- Don't use saturated colors as page backgrounds — the 9 pastels are all near-greyscale
- Don't hard-code Inter — users can switch to Lora (serif) or IBM Plex Mono
- Don't surface block handles always — the "invisible" design is the signature
- Don't use pure black (`#000000`) for body text — `#37352F` warm dark gray is correct
- Don't add primary-color CTAs ("Add", "Save" should be black or ghost) — Notion doesn't have a brand accent
- Don't animate aggressively — motion is quiet, 150-300ms ease-out range
- Don't use shadow on content blocks — shadows are reserved for floating panels only
- Don't bold "Body" paragraphs by default — body is 400 weight
- Don't use column layouts without user request — Notion's defaults are single-column top-to-bottom
- Don't surface the full block menu on tap — tap inserts cursor; long-press opens handles; tap ⋮⋮⋮ opens menu

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cover image 100pt tall; sidebar is 80% screen width |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Cover image 140pt tall; larger emoji |
| iPad (portrait) | 768pt | 2-column: sidebar always visible (280pt) + content (centered max 760pt) |
| iPad (landscape) | 1024pt+ | Same 2-column; content max 800pt centered |

### Dynamic Type
- Page title, headings, body, captions: full scale
- Block handles, tab labels, sidebar labels, mention chips, command palette option text: FIXED (layout-sensitive)
- Code blocks: scale to XL (but stay monospace)

### Orientation
- All pages support rotation
- iPad adds split-view controller: primary sidebar (280pt) + secondary page content
- Video/image blocks rotate freely

### Touch Targets
- Block handle (⋮⋮⋮): 28pt hit, 20pt glyph
- Tab bar icons: 24pt glyph, 44pt hit
- Sidebar row: 32pt tall, full-row tap
- Toggle chevron: 16pt glyph, 44pt hit
- Primary buttons: 36pt tall minimum

### Safe Area Handling
- Top: top nav respects safe area and Dynamic Island
- Bottom: bottom tab + home indicator respected
- Keyboard: page body scrolls above keyboard; command palette positions above keyboard when triggered
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#191919`
- Surface gray: `#F7F6F3`
- Divider: `#EAECEC`
- Text primary: `#37352F` light / `#E6E6E4` dark
- Text secondary: `#9B9A97`
- Link blue: `#2E75CC`
- Primary button (Notion Black): `#000000`
- Pastel page bg (gray): `#F1F1EF`
- Pastel page bg (yellow): `#FBF3DB`
- Pastel page bg (blue): `#E7F3F8`
- Pastel page bg (green): `#EDF3EC`
- Pastel page bg (red): `#FDEBEC`
- Callout text (orange): `#D9730D`
- Callout text (green): `#0F7B6C`
- Callout text (blue): `#0B6E99`
- Callout text (red): `#E03E3E`

### Example Component Prompts
- "Build the Notion page header in SwiftUI: 120pt full-bleed cover image at top (optional — if no cover, skip this row). Below: 64pt emoji icon at leading 16pt with -32pt top offset (overlaps cover). Below emoji: page title in Inter 32pt weight 700 `#37352F`, 16pt horizontal inset. Below title: sub-actions row with small ghost buttons 'Add icon', 'Add cover', 'Add comment' — Inter 13pt w400 `#9B9A97`, 8pt horizontal padding, no background, visible only when page is empty."

- "Create a Notion paragraph block: 16pt horizontal insets, 6pt bottom margin, Inter 16pt weight 400 `#37352F`. On long-press (400ms), fade-in leading block handle ⋮⋮⋮ (20pt `#9B9A97` at 50% opacity) + `+` insert-below icon, positioned at -24pt left of block. Empty-state placeholder: 'Type ''/'' for commands' in `#9B9A97`."

- "Render the Notion `/` command palette: floating panel anchored at cursor. 280pt wide (or full-width minus 16pt on phone), `#FFFFFF` bg, 1pt `#EAECEC` border, 6pt corner radius, shadow `rgba(0,0,0,0.08) 0 4px 12px`. Max height 360pt scroll. Each row: 40pt tall, leading 20pt block-type icon, title in Inter 14pt w500 `#37352F`, subtitle in Inter 12pt w400 `#9B9A97`. Up/down arrows navigate; selected row: `#EFEEE9` background. Tap or Enter inserts block. Dismiss: fade-out + 4pt slide-up in 150ms."

- "Build a Notion callout block: full-width minus 16pt insets, 16pt all padding, 4pt corner radius, background one of the pastel page colors (default yellow `#FBF3DB` with text `#DFAB01`, or blue `#E7F3F8` with `#0B6E99`, etc.). Leading 20pt emoji glyph (user-picked). Content area: Inter 16pt w400 in the matching callout text color. No border."

- "Create a Notion toggle block: leading chevron ▸ (16pt `#9B9A97`) at 4pt gap from text, text in Inter 16pt w500 `#37352F`. On tap: chevron rotates 90° to ▾ over 150ms, children slide down from height 0 → auto over 200ms ease-out. Children indented 24pt from left. Soft haptic on toggle."

- "Render the Notion sidebar: 280pt wide, `#F7F6F3` background, slides in from left with 250ms ease-out when swipe-right or hamburger tapped. Header: 48pt workspace row with 24pt workspace icon + name + chevron. Sections: 'Teamspaces', 'Shared', 'Private'. Each page row: 32pt tall, leading 16pt emoji/page-icon, title in Inter 14pt w500 `#37352F`, trailing ⋯ menu. Active page: `#EFEEE9` background. Nested children indented 24pt per level with leading chevron."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) in light, charcoal `#191919` in dark — NOT true black, NOT off-white
2. Text primary is warm gray `#37352F` — NOT pure black; body at 400 weight, headings at 700
3. Page-background pastels are all desaturated near-greyscale (gray/brown/orange/yellow/green/blue/purple/pink/red) — NOT bright
4. Block handles (⋮⋮⋮ + `+`) appear only on long-press/hover — "invisible until needed" is the signature
5. Typography is user-switchable: default Inter, optional Lora (serif) or IBM Plex Mono — respect user setting globally
6. The `/` command palette is the primary block-creation gesture — a floating 280pt panel anchored at cursor
7. Blocks flow top-to-bottom with 6pt gap — no cards, no borders, no shadows on content
8. Callouts use pastel backgrounds + leading emoji, text in matching saturated color — NEVER with borders
9. Primary CTAs are Notion Black (`#000000`) — the app has no brand accent color
10. Mentions (@person / @page) are inline chips with avatar + 10% blue background + blue text — never plain inline text
