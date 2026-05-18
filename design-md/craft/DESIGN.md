# Design System Inspiration of Craft (iOS)

## 1. Visual Theme & Atmosphere

Craft's iOS app is a *beautifully crafted document editor* — the antithesis of a spreadsheet-y productivity tool. Where most note apps feel like text fields with chrome bolted on, Craft feels like a native iOS app that a designer obsessed over: rounded everything, soft layered shadows, fluid spring physics, and a signature blue→purple gradient that threads through every primary action. The canvas is warm off-white (`#FCFCFD`) in light, a soft near-charcoal (`#1A1A1E`) in dark — never pure black, never clinical. Open a document and you see a hero zone (optional full-bleed cover, a large emoji or photo icon, the title in Inter 28pt extrabold), then a vertical flow of **blocks** — but unlike Notion's invisible-flat blocks, Craft's blocks frequently render as *cards*: rounded rectangles with a subtle border and a soft drop shadow, with an icon chip on the left. Nested pages, link previews, toggles, and to-dos all become tappable cards. This card-forward block model is Craft's signature visual move.

The brand color is unmistakable: a **blue→purple gradient** running roughly `#2F5BEA` (Craft Blue) to `#6E56CF` (Craft Purple). It appears on the "New Document" button, the floating `+` action, the active bottom-bar icon, selected-state fills, focused input borders, and the app icon itself. Unlike Notion (which has *no* brand accent), Craft leans into this gradient as identity — it's the one constant across light and dark. Solid `#2F5BEA` is used where a gradient would be visually noisy (small toggles, links, inline accents).

The second signature is **Daily Note** — a date-stamped document auto-created each day, surfaced as a card and as a dedicated tab/entry point. It's Craft's opinion that journaling and daily capture deserve a first-class home. Combined with **block actions** (long-press or the leading handle reveals Style, Turn into, Move, Link to, Delete) and the **`/` block inserter** (type `/` to open a menu of block types — Text, Page, To-do, Image, Code, Table, Toggle), these three affordances — cards, Daily Note, `/` inserter — are how everything in Craft gets built.

Typography is **Inter** as the primary UI/document face (Craft historically shipped a refined sans very close to Inter; Inter is the faithful, freely-licensed stand-in), falling back to SF Pro on iOS. Hierarchy is editorial: document title at 28pt extrabold, H1 24pt bold, H2 20pt bold, H3 17pt semibold, body 16pt regular. Generous corner radii (10–20pt on cards, sheets, and buttons) and soft, low-opacity shadows give the whole app a tactile, "objects you can pick up" feel — far softer than Material, warmer than stock iOS.

**Key Characteristics:**
- Warm off-white canvas (`#FCFCFD`) light / soft charcoal (`#1A1A1E`) dark — never pure black
- Signature **blue→purple gradient** (`#2F5BEA` → `#6E56CF`) on every primary action — the constant brand identity
- **Card-forward blocks** — nested pages, links, toggles, to-dos render as rounded cards with icon chips + soft shadows
- **Daily Note** — auto-created date-stamped document, surfaced as a card and a dedicated entry point
- **`/` block inserter** — type `/` to insert any block type (Text, Page, To-do, Image, Code, Table, Toggle)
- **Block actions** — long-press / leading handle → Style, Turn into, Move, Link to, Delete
- Optional full-bleed **cover + emoji/photo page icon** hero zone, generous and editorial
- Cross-document **links & backlinks** — pages reference each other as inline cards
- Generous corner radii (10–20pt) + soft layered shadows — tactile, "pick-it-up" feel
- Fluid spring motion — sheets, cards, and the `/` menu animate with iOS-native spring physics
- Minimal chrome: compact top bar (back · search · `+` · ⋯), a slim bottom bar with center gradient FAB

## 2. Color Palette & Roles

### Primary (Interactive)
- **Craft Blue** (`#2F5BEA`): Primary solid interactive — links, toggles, focused borders, selected states, gradient start.
- **Craft Blue Pressed** (`#2347C9`): Pressed state for solid blue controls.
- **Craft Purple** (`#6E56CF`): Gradient end; secondary accent on highlights and decorative chips.
- **Craft Purple Soft** (`#8B73E8`): Lighter purple for dark-mode accents and hover tints.
- **Brand Gradient**: linear `#2F5BEA → #6E56CF` (≈120°) — New Document button, FAB, active tab, hero cover default.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FCFCFD`): Primary light canvas — warm off-white, not pure white.
- **Surface Gray** (`#F4F4F6`): Sidebar / space list background, subtle elevated rows.
- **Card Surface** (`#FFFFFF`): Block-card background, sheets, popovers (lifts above the warm canvas).
- **Surface Pressed** (`#ECECEF`): Pressed/selected rows and cards.
- **Divider** (`#E6E6EA`): Hairline borders on cards and between sections.
- **Selection Highlight** (`#2F5BEA29`): ~16% blue overlay on selected text/blocks.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1A1A1E`): Primary dark canvas — Craft's soft charcoal, NOT pure black.
- **Dark Card** (`#232328`): Block-card background, sheets, popovers.
- **Dark Surface 2** (`#2C2C32`): Hovered/pressed cards, secondary fills.
- **Dark Divider** (`#34343C`): Hairline borders on dark.
- **Dark Selection** (`#2F5BEA3D`): ~24% blue overlay on dark selection.

### Text
- **Text Primary Light** (`#1C1C22`): Primary body/title text — near-black, slightly warm.
- **Text Secondary Light** (`#6A6A78`): Metadata, captions, secondary body.
- **Text Tertiary Light** (`#9B9BA6`): Placeholder, disabled, very low-emphasis.
- **Text Primary Dark** (`#ECECEF`): Primary text on dark — soft white.
- **Text Secondary Dark** (`#9B9BA6`): Secondary on dark (shared tone, works on both).
- **Text Tertiary Dark** (`#67677A`): Placeholder/disabled on dark.

### Block Accent Tints (icon chips & callouts)
Craft tints block-card icon chips and callouts with low-opacity brand/utility colors.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Blue Tint | `#2F5BEA14` | `#2F5BEA2E` |
| Purple Tint | `#6E56CF14` | `#6E56CF33` |
| Green Tint | `#30A46C1F` | `#30A46C33` |
| Amber Tint | `#F0A92B24` | `#F0A92B33` |
| Red Tint | `#E5484D1F` | `#E5484D33` |
| Neutral Tint | `#7676801F` | `#8B8B9E29` |

### Highlight / Callout Colors (saturated)
For inline text highlights, callout accents, and tag chips.

| Role | Color |
|------|-------|
| Blue | `#2F5BEA` |
| Purple | `#6E56CF` |
| Green | `#30A46C` |
| Amber | `#F0A92B` |
| Red | `#E5484D` |
| Pink | `#D6409F` |
| Teal | `#12A594` |
| Gray | `#7E7E8A` |

### Semantic
- **Error Red** (`#E5484D`): Delete confirmation, error toast, destructive actions.
- **Success Green** (`#30A46C`): Completed to-do fill, save-success toast.
- **Warning Amber** (`#F0A92B`): Warning callouts, sync-pending indicator.
- **Link Blue** (`#2F5BEA`): Inline links and cross-document references.
- **Focus Ring** (`#2F5BEA`): 2pt focused input/border ring.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist sans; faithful, freely-licensed stand-in for Craft's bespoke product sans, optimized for UI and long-form reading at small sizes.
- **Serif Option (reading mode)**: `Lora` (Google Fonts, SIL OFL) — optional serif for a "book"-style reading preference on long documents.
- **Mono (code blocks)**: `JetBrains Mono` (SIL OFL) — used only inside code blocks and inline code.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS behavior**: falls back to SF Pro Display (titles) / SF Pro Text (body) when Inter isn't bundled.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Doc Title | Inter | 28pt | 800 | 1.2 | -0.5pt | Below cover/emoji, extrabold hero |
| Heading 1 (`/h1`) | Inter | 24pt | 700 | 1.25 | -0.3pt | Top-level section in page |
| Heading 2 (`/h2`) | Inter | 20pt | 700 | 1.3 | -0.2pt | Sub-section |
| Heading 3 (`/h3`) | Inter | 17pt | 600 | 1.35 | -0.1pt | Sub-sub-section |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Paragraph block default |
| Body Emphasis | Inter | 16pt | 600 | 1.55 | 0pt | Bold inline run |
| Card Title | Inter | 15pt | 600 | 1.3 | 0pt | Block-card primary label |
| Card Subtitle | Inter | 12pt | 400 | 1.35 | 0pt | Block-card metadata line |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | "Edited 2m ago", doc subtitle |
| Caption | Inter | 12pt | 500 | 1.4 | 0.1pt | Chips, small labels |
| Quote | Lora | 17pt | 400 | 1.5 | 0pt | Italic quote block |
| Code Inline | JetBrains Mono | 14pt | 400 | 1.4 | 0pt | `inline code` with tint bg |
| Code Block | JetBrains Mono | 13pt | 400 | 1.5 | 0pt | Fenced code block |
| Button | Inter | 15pt | 600 | 1.0 | 0pt | Primary/secondary buttons |
| Link | Inter | 16pt | 500 | 1.55 | 0pt | Inline link — `#2F5BEA` |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom-bar labels (when shown) |
| Sidebar Label | Inter | 14pt | 500 | 1.3 | 0pt | Space/document list rows |
| Placeholder | Inter | 16pt | 400 | 1.55 | 0pt | "Type / for blocks" |

### Principles
- **Editorial rhythm**: sizes/weights follow document conventions (Title > H1 > H2 > H3 > body), not app-chrome conventions.
- **Extrabold reserved for the doc title**: 800 weight is the single most expressive moment; everything else tops out at 700.
- **Body breathes**: 16pt body at 1.55 line-height — Craft documents are made to be *read*, not skimmed.
- **Monospace is code-only**: JetBrains Mono appears solely in code blocks and inline code.
- **Dynamic Type first-class**: title, headings, body, captions scale; card subtitles, tab labels, slash-menu rows stay fixed for layout safety.

## 4. Component Stylings

### Buttons

**Primary Button (New Document / Done / Share)**
- Shape: Rounded rectangle, 12pt corner radius
- Background: gradient `#2F5BEA → #6E56CF` (≈120°)
- Text: `#FFFFFF`, Inter 15pt weight 600
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `filter: brightness(0.92)` + scale 0.98 + soft haptic
- Optional leading SF Symbol 15pt, 8pt gap

**Solid Button (Secondary primary)**
- Background: solid `#2F5BEA` (`#2347C9` pressed)
- Text: `#FFFFFF`, Inter 15pt weight 600
- Same shape/padding as primary; used where a gradient would be visually noisy at small size

**Secondary / Outline Button**
- Background: transparent
- Text: `#1C1C22` light / `#ECECEF` dark
- Border: 1pt `#E6E6EA` / `#34343C`
- Corner radius: 10pt
- Padding: 12pt vertical, 20pt horizontal
- Font: Inter 14pt weight 500
- Pressed: background `#ECECEF` / `#2C2C32`

**Ghost / Text Button (Add block / Cancel)**
- Background: transparent, no border
- Text: `#2F5BEA` (action) or `#6A6A78` (Cancel)
- Font: Inter 14pt weight 600
- Pressed: 0.6 opacity

**Floating Action Button (`+`)**
- Size: 46pt, 14pt corner radius (rounded square, NOT a circle)
- Background: gradient `#2F5BEA → #6E56CF`
- Glyph: `plus` 22pt `#FFFFFF`
- Shadow: `0 8px 20px -6px rgba(47,91,234,0.6)`
- Centered in the bottom bar; opens the new-block / new-document sheet

**Icon Button (top bar / block handle)**
- Size: 22pt glyph in a 44pt hit area
- Default color: `#6A6A78` / `#9B9BA6`
- Pressed: `#2F5BEA`

### Blocks (the core atom)

Every content unit is a "block". Blocks support: leading drag/handle (long-press reveal), `/` insert, drag-reorder, and a Style/Turn-into menu. Many block types render as **cards**.

**Paragraph Block**
- Content: body 16pt w400 `#1C1C22`
- Empty placeholder: "Type / for blocks" in `#9B9BA6`
- Block gap: 8pt vertical

**Heading 1 / 2 / 3 Block**
- 24 / 20 / 17pt, weights 700 / 700 / 600
- 16pt top margin, 6pt bottom margin

**Card Block (Page / Link / nested document)** — *signature*
- Layout: rounded card, 12pt radius, 1pt `#E6E6EA` border, soft shadow `0 6px 18px -10px rgba(0,0,0,0.10)` light / `…0.6` dark
- Leading icon chip: 36pt rounded square (9pt radius) with tinted bg (e.g. blue tint) + emoji/favicon
- Title: Inter 15pt w600; subtitle: Inter 12pt w400 `#6A6A78`
- Trailing chevron `chevron.right` 16pt `#9B9BA6`
- Padding: 14pt all; tap opens the page / link

**To-do Block**
- Leading checkbox: 18pt rounded square (6pt radius), 1.8pt `#9B9BA6` border
- Checked: fill `#2F5BEA`, white checkmark, label gets strikethrough + `#9B9BA6`
- Content: 16pt w400; tap circle toggles with spring + soft haptic

**Toggle Block** (collapsible)
- Leading chevron ▸ (closed) / ▾ (open) 14pt `#9B9BA6`
- Title: 16pt w500; children indented 20pt; expand 220ms spring

**Callout Block**
- Background: a brand-tint fill (e.g. blue tint `#2F5BEA14`)
- Leading emoji 20pt; content 16pt w400
- Corner radius: 12pt; padding: 14pt; no border

**Code Block**
- Background: `#F4F4F6` light / `#2C2C32` dark
- Font: JetBrains Mono 13pt; 14pt padding; 10pt radius
- Language label top-right `#9B9BA6` 12pt; copy button top-right

**Quote Block**
- Left border: 3pt `#6E56CF`; padding 2pt/14pt; Lora 17pt italic `#6A6A78`

**Divider Block**
- 1pt `#E6E6EA` line, 16pt vertical margin

**Image / Photo Block**
- Full-width minus 22pt insets; 12pt corner radius; optional caption 12pt `#9B9BA6` below

**Table Block**
- Inline grid; header row w600; 1pt `#E6E6EA` cell borders; horizontal scroll within block

### Page Structure

**Page Header (hero)**
- Optional full-bleed cover, 96pt mobile / 160pt tablet, 14pt corner radius (insets) or full-bleed
- Page icon: 40pt emoji OR photo, with -38pt top offset overlapping cover
- Title: Inter 28pt w800 `#1C1C22`, 22pt horizontal inset, 6pt top margin from icon
- Subtitle row: "Edited 2m ago · Shared with N" — Inter 13pt `#6A6A78`

**Page Body**
- 22pt horizontal insets; vertical flow of blocks; 8pt block gap
- Empty state: "Type / for blocks" placeholder in first block

### Navigation

**Top Bar (Document)**
- Height: 44pt + safe area
- Leading: back chevron `chevron.left` + section name (e.g. "Home")
- Trailing: search · `+` (insert) · ⋯ (page actions: Share, Style, Move, Export, Delete)
- No large title — the document's own hero title is the title

**Bottom Bar**
- Height: 64pt + safe area
- Background: `#FCFCFD` / `#1A1A1E` @ 94% with blur, 0.5pt top divider
- Layout: left group (Home, Search) · center gradient FAB (`+`) · right group (Documents, Profile)
- Active icon: solid `#2F5BEA` (no pill); inactive `#6A6A78` / `#9B9BA6`
- Labels: optional Inter 10pt w500

**Space / Document List (slide-in or root)**
- Background: `#F4F4F6` / `#1A1A1E`
- Rows: 44pt tall, leading 20pt emoji/icon, title Inter 14pt w500, trailing ⋯
- Active row: `#ECECEF` / `#2C2C32` background
- Sections: "Spaces", "Recent", "Daily Notes", "Shared"; nested pages indent 20pt with leading chevron

### Input Fields

**Search Bar**
- Height: 40pt; background `#F4F4F6` / `#2C2C32`; no border; 10pt radius
- Leading `magnifyingglass` 16pt `#9B9BA6`; placeholder "Search" 15pt `#9B9BA6`
- Focus: 2pt `#2F5BEA` ring

**Text Field (rename / new doc)**
- Height: 44pt; background `#FFFFFF` / `#232328`; 1pt `#E6E6EA` / `#34343C`; 10pt radius
- Focus: border → 2pt `#2F5BEA`; 14pt horizontal padding; Inter 16pt

### Distinctive Components

**Block Card** — the signature: nested pages, link previews, toggles, and to-dos all become rounded icon-chip cards with soft shadows. This card-forward model (vs. Notion's flat blocks) is Craft's identity.

**Daily Note** — a date-stamped document auto-created each day, surfaced as a dedicated card ("Daily Note — May 18") and an entry point. Tapping creates/opens today's note; it accrues blocks like any page.

**`/` Block Inserter** — type `/` at the start of a line to open a cursor-anchored sheet/popover of block types. Each row: 28pt icon chip + title (Inter 13pt w600) + subtitle (12pt `#9B9BA6`). Selected row: blue tint. Tap or Return inserts.

**Block Actions Menu** — long-press a block or tap its leading handle to open: Style, Turn into, Copy link, Duplicate, Move to, Link to page, Delete. Bottom sheet on phone.

**Cross-Document Link Card** — typing `@` or pasting a Craft link inserts an inline card with the linked page's icon + title; backlinks surface at the page bottom.

**Cover + Icon Hero** — optional full-bleed cover image (Unsplash / upload / gradient) with a large emoji or photo icon overlapping it; the page's expressive identity moment.

**Style Sheet** — a bottom sheet for text/page styling: card style toggle, full-width toggle, text size, page background, font (sans / serif). Spring-presented.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 16, 20, 24, 32, 40, 48, 64
- Block vertical gap: 8pt
- Heading top margin: 16pt
- Page content inset: 22pt horizontal (slightly generous — editorial feel)
- Card internal padding: 14pt

### Grid & Container
- iPhone: content at 22pt horizontal insets; cover full-bleed or inset (14pt radius)
- iPad: content max-width 720pt centered, 32pt insets; sidebar 280pt always visible in landscape
- Block cards span full content width
- Tables scroll horizontally within their block

### Whitespace Philosophy
- **Editorial breathing room**: 8pt between blocks, 16pt before headings, 22pt page insets — generous, book-like
- **Cards float, text flows**: structural blocks (pages, links, toggles) get card framing + shadow; prose blocks flow inline with no chrome
- **Hero is optional but expressive**: cover + icon zone can be tall and image-rich, or absent (0pt)
- **Soft, never harsh**: every container uses ≥10pt radius; nothing is a hard 90° rectangle except dividers/full-bleed covers

### Border Radius Scale
- Square (0pt): Dividers, full-bleed covers
- Subtle (6pt): Checkboxes, small chips, inline code bg
- Comfortable (10pt): Outline buttons, search bar, text fields, icon chips
- Standard (12pt): Block cards, callouts, primary buttons, image blocks
- Large (16–20pt): Bottom sheets, modals, FAB
- Pill (500pt): Tags, segmented toggles
- Circle (50%): Avatars

## 6. Depth & Elevation

Craft uses depth deliberately and softly — shadows are low-opacity and diffuse, signaling "this is a liftable object," never harsh Material elevation.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Prose blocks, dividers, list rows |
| Card (Level 1) | `0 6px 18px -10px rgba(0,0,0,0.10)` light / `…0.6` dark | Block cards (page/link/toggle/to-do), callouts |
| Floating (Level 2) | `0 12px 32px -12px rgba(0,0,0,0.14)` light / `…0.7` dark | `/` inserter, block-actions menu, popovers |
| Sheet (Level 3) | `0 -10px 40px rgba(0,0,0,0.18)` | Bottom sheets (Style, Share, New) |
| FAB | `0 8px 20px -6px rgba(47,91,234,0.6)` | Center gradient FAB (brand-tinted shadow) |
| Modal Overlay | `rgba(0,0,0,0.32)` | Dim behind sheets/modals |

**Shadow Philosophy**: Craft's shadows are soft and brand-aware — the FAB casts a *blue-tinted* shadow, reinforcing the gradient identity. On dark mode, shadows deepen (0.6–0.7) and a 1pt divider border is added to floating panels for an elevation cue. Depth is tactile (objects you can pick up), not flat (Notion) and not heavy (Material).

### Motion
- **Block reorder (drag)**: 60fps lift with slight scale-up (1.03) + shadow bloom; neighbors shift over 200ms ease-out; drop snaps with spring (response 0.30, damping 0.82) + soft haptic
- **Card tap → open page**: push transition 320ms with subtle parallax (iOS native push)
- **`/` inserter**: fade-in + 6pt slide-up in 180ms ease-out; dismiss fade + slide-down 150ms; arrow/scroll selection instant
- **Toggle expand/collapse**: chevron rotates 90° in 180ms; children expand height 0→auto with spring (response 0.32, damping 0.85)
- **To-do complete**: checkbox fills with a 220ms spring scale-pop + soft haptic; label strikes through over 150ms
- **Sheet present (Style / Share / New)**: slides up 360ms spring (response 0.40, damping 0.82), dim fades in
- **Sidebar / space list**: edge-swipe 1:1 tracking; release threshold 50% → spring open/close (damping 0.80)
- **FAB press**: scale 0.94 + brightness 0.92, releases with spring overshoot
- **Page save (auto)**: silent — no motion; "Saved" / "Syncing" pill only on conflict or manual save
- **Haptic feedback**: soft impact on to-do complete, toggle, block drag start, `/` open, sheet present

## 7. Do's and Don'ts

### Do
- Use warm off-white (`#FCFCFD`) light / soft charcoal (`#1A1A1E`) dark — NEVER pure white or pure black
- Carry the blue→purple gradient (`#2F5BEA → #6E56CF`) on every primary action — it's the constant identity
- Render structural blocks (pages, links, toggles, to-dos) as rounded cards with icon chips + soft shadows
- Make the FAB a rounded square (14pt radius), not a circle, with a blue-tinted shadow
- Surface Daily Note as a first-class card and entry point
- Use the `/` inserter as the primary block-creation gesture
- Give everything generous radius (≥10pt) and soft, diffuse, low-opacity shadows
- Use solid `#2F5BEA` where a gradient would be visually noisy at small size (toggles, links, ring)
- Animate with iOS-native spring physics — fluid, slightly bouncy, never linear
- Keep the doc title at 28pt extrabold (800) — the single most expressive type moment
- Keep prose blocks chrome-free; reserve card framing for structural blocks

### Don't
- Don't use pure black (`#000000`) for canvas or text — `#1A1A1E` / `#1C1C22` are correct
- Don't render every block as a card — prose flows inline; only structural blocks get card framing
- Don't make the FAB a circle — Craft's FAB is a rounded square
- Don't use a flat single accent color where the gradient belongs (primary buttons, FAB, active tab)
- Don't use hard 90° corners on containers — Craft is soft everywhere except dividers/covers
- Don't use heavy Material-style drop shadows — keep them diffuse and low-opacity
- Don't animate linearly or abruptly — motion is spring-based and fluid
- Don't bold body paragraphs by default — body is 400 weight
- Don't add a tint pill behind the active tab — active is just solid `#2F5BEA`
- Don't bury Daily Note — it's a signature, give it a visible home
- Don't hardcode multi-column layouts — Craft defaults to single-column document flow

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cover 80pt; space list is 86% screen width |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Cover 120pt; larger emoji icon |
| iPad (portrait) | 768pt | 2-column: sidebar (280pt) + content (centered max 720pt) |
| iPad (landscape) | 1024pt+ | 3-pane optional: spaces · document list · editor |

### Dynamic Type
- Doc title, headings, body, quote, captions: full scale
- Card subtitles, tab labels, slash-menu rows, sidebar labels: FIXED (layout-sensitive)
- Code blocks: scale to XL but stay monospace

### Orientation
- All screens rotate; iPad gains split-view (sidebar + editor)
- Cover/image blocks reflow to new width; hero recomputes height

### Touch Targets
- To-do checkbox: 18pt glyph, 44pt hit
- Block handle: 22pt glyph, 44pt hit
- Bottom-bar icons: 21pt glyph, 44pt hit
- FAB: 46pt (already ≥44pt)
- Sidebar row: 44pt tall, full-row tap
- Primary buttons: ≥44pt tall

### Safe Area Handling
- Top: top bar respects safe area + Dynamic Island
- Bottom: bottom bar + home indicator respected; FAB sits above home indicator
- Keyboard: editor scrolls above keyboard; `/` inserter positions above keyboard
- Sides: 22pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FCFCFD`
- Canvas (dark): `#1A1A1E`
- Card (light): `#FFFFFF` · Card (dark): `#232328`
- Surface gray (light): `#F4F4F6`
- Divider: `#E6E6EA` light / `#34343C` dark
- Text primary: `#1C1C22` light / `#ECECEF` dark
- Text secondary: `#6A6A78` light / `#9B9BA6` dark
- Craft Blue (solid): `#2F5BEA`
- Craft Purple: `#6E56CF`
- Brand gradient: `#2F5BEA → #6E56CF` (≈120°)
- Success green: `#30A46C`
- Warning amber: `#F0A92B`
- Error red: `#E5484D`
- Blue tint (icon chip): `#2F5BEA14` light / `#2F5BEA2E` dark

### Example Component Prompts
- "Build the Craft document header in SwiftUI: optional 96pt full-bleed cover (14pt corner radius), a 40pt emoji icon with -38pt top offset overlapping the cover at 22pt leading, then the title in Inter 28pt weight 800 `#1C1C22` at 22pt horizontal inset, and a subtitle row 'Edited 2m ago · Shared with 4' in Inter 13pt `#6A6A78`."

- "Create a Craft Card Block: rounded card 12pt radius, 1pt `#E6E6EA` border, soft shadow `0 6px 18px -10px rgba(0,0,0,0.10)`. Leading 36pt rounded-square icon chip (9pt radius) with a blue tint background `#2F5BEA14` and an emoji. Title in Inter 15pt w600 `#1C1C22`, subtitle in Inter 12pt w400 `#6A6A78`, trailing `chevron.right` 16pt `#9B9BA6`. 14pt padding. Tap pushes the nested page with 320ms parallax."

- "Render the Craft `/` block inserter: cursor-anchored floating panel, 12pt corner radius, `#FFFFFF` bg light / `#232328` dark, shadow `0 12px 32px -12px rgba(0,0,0,0.14)`, 1pt `#34343C` border in dark. Rows 44pt: leading 28pt icon chip, title Inter 13pt w600 `#1C1C22`, subtitle Inter 12pt w400 `#9B9BA6`. Selected row background blue tint `#2F5BEA14`. Fade-in + 6pt slide-up 180ms; tap or Return inserts the block."

- "Build the Craft primary button: gradient `#2F5BEA → #6E56CF` at ≈120°, 12pt corner radius, Inter 15pt w600 `#FFFFFF`, 13pt vertical / 26pt horizontal padding. Pressed: brightness 0.92 + scale 0.98 + soft haptic."

- "Create the Craft to-do block: leading 18pt rounded-square checkbox (6pt radius, 1.8pt `#9B9BA6` border). On tap, fill `#2F5BEA` with a white checkmark using a 220ms spring scale-pop + soft haptic; label gets strikethrough and `#9B9BA6` over 150ms. Label in Inter 16pt w400."

- "Render the Craft bottom bar: 64pt + safe area, `#1A1A1E` @ 94% with blur and a 0.5pt `#34343C` top divider. Left group (Home, Search), a center 46pt rounded-square FAB (14pt radius) with the brand gradient and a blue-tinted shadow `0 8px 20px -6px rgba(47,91,234,0.6)`, right group (Documents, Profile). Active icon solid `#2F5BEA`, inactive `#9B9BA6`, no tint pill."

- "Build the Craft Daily Note card: a block card titled 'Daily Note — May 18' with a 📅 emoji in a purple-tint chip `#6E56CF14`, subtitle 'Auto-created · 3 blocks', trailing chevron. Tapping opens (or creates) today's date-stamped document."

### Iteration Guide
1. Canvas is warm off-white `#FCFCFD` light / soft charcoal `#1A1A1E` dark — NOT pure white/black
2. The blue→purple gradient `#2F5BEA → #6E56CF` is the constant identity — every primary action carries it
3. Structural blocks (pages, links, toggles, to-dos) render as rounded cards with icon chips + soft shadows; prose flows chrome-free
4. The FAB is a rounded square (14pt radius) with a blue-tinted shadow — never a circle
5. Daily Note is a first-class card + entry point — surface it prominently
6. The `/` inserter is the primary block-creation gesture — cursor-anchored floating panel
7. Everything is soft: ≥10pt radius on containers, diffuse low-opacity shadows
8. Motion is iOS-native spring — fluid and slightly bouncy, never linear
9. Doc title is 28pt extrabold (800) — the single most expressive type moment; body is 16pt/400 at 1.55
10. Use solid `#2F5BEA` for small accents (toggles, links, focus ring); reserve the gradient for primary buttons/FAB/active tab
